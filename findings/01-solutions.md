# 01. DentBird Solutions ① — 런타임 Config + 격리환경 재현 (인프라 플랫폼화) — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (런타임config·격리재현 광범위 발굴 완료 / 기여 주체 일부 확인 필요)
> 발굴 대상: `~/Works/devops/dentbird-solutions`, vault `projects/dentbird-solutions/`
>
> **주제 분리 안내**: 01 Solutions는 양이 방대해 주제별 findings로 나눔. 이 파일 = **런타임 Config + 격리환경 재현**. 형제 카드: 09(E2E·EC2 Claude 자동화), 10(인증/보안: JWT→cookie·암복호화), 08(iwtk→Three.js), Datadog·앱/서버 통합 등은 추후. ([서비스 개요](./_service-overview.md)에 팀 차원 기술 별도 정리 예정)
>
> **★ 기여 주체 표기 규칙**: `[본인]` = 본인 커밋/기여 확정, `[팀]` = 팀/타인 주도(서비스 이해용·면접 맥락), `[기여확인]` = 분리 필요. 이력서 본문엔 `[본인]`만, 면접 "서비스 설명"엔 `[팀]`도 활용.

---

## A. 런타임 Config — "Build Once, Deploy Everywhere"

### 1. 의사결정 흔적
- **문제(전환 동기)**: 빌드타임 env 주입 방식에서 **QA/prod 배포 산출물에 dev 환경변수가 박히는 버그**. 근본 원인 = NX가 startup 시 모든 `vite.config.ts`를 평가하며 `dotenv.config()`가 `process.env`를 오염시키고, child build process가 이를 상속. [code: docs/handoff-runtime-config.md] [git: 본인 — `fix(linker): dotenv override: true 제거 — Prod 빌드 시 .env가 파이프라인 환경변수를 덮어쓰는 문제 해결`, 그리고 복원 커밋]
- **결정**: 빌드타임 bake-in 제거 → **런타임에 `config.js`로 환경변수 주입**. 빌드 산출물을 환경 무관하게 **1회만 빌드**, 배포 시 `config.js`만 환경별로 재생성·교체. [code: scripts/generate-config-js.mjs, .github/workflows]

### 2. 측정 가능한 변화
- 환경별(dev/qa/prod) 프론트엔드 **빌드 파이프라인 제거 → 단일 빌드**. CI 빌드 시간 절감(문서상 "3배 감축" 표현 — 3회 빌드→1회). [code: .github/workflows/build-client.yml] [측정필요: 정확한 시간값]
- `BASE_DOMAIN` 1개 → `deriveUrls()`가 **약 80개 URL 자동 파생**(backend/gateway/AI 서버, 앱별 URL, 멀티테넌트 호스트). 환경 고유값은 ~30개로 축소. [code: libs/runtime-config/src/types.ts]
- 앱별 전환 규모: account-client 55건, cloud-desktop/mobile 113건, batch-web 30건의 `process.env.NX_PUBLIC_*` → `getConfig()` 교체. [code/git: PR #6422~#6469, 2026-04-14 완료]

### 3. 후속 영향
- **이 런타임 config가 "격리환경 커밋 재현"의 기반** (B 섹션). 같은 번들 + env만 교체 → 임의 커밋/환경 재현 가능.
- 타입 안전성 향상: `process.env.X as string`(단언) → `getConfig()` 반환 `Readonly<DentbirdConfig>`(자동완성·빌드타임 타입체크). [code: libs/runtime-config/src/config-store.ts]
- host/path 감지 중앙화로 확장(`detectProduct()`/`getTenantName()`/`getBasePath()`/`getCookieDomainAttribute()`). [code: docs/design/runtime-config-host-detection.md, PR #7013]

### 메커니즘 요약 (코드)
- 전역 주입점 `window.__DENTBIRD_CONFIG__` ← `index.html`의 동기 `<script src="config.js">`. Electron은 webpack DefinePlugin 치환. [code: libs/runtime-config/src/global.d.ts]
- 싱글톤 `getConfig()`: window config 우선 → 없으면 `process.env` 폴백(전환기) → freeze 캐싱. [code: config-store.ts]
- 정본 소스 `deploy/config/{dev,qa,prod,test}.json` → `generate-config-js.mjs <env>`가 `config.js` 생성(GTM 인라인 포함). [code]
- dev: vite `runtime-config-dev` 플러그인이 `/config.js` 요청을 가로채 `local.json`/`dev.json` 서빙. [code: apps/*/vite.config.ts]
- Docker: `docker/stack/lib/runtime-config.sh`의 `bust_runtime_config_cache()`가 config 변경 시 `config.js?v=HASH` 캐시버스터 주입. [code]

### ★ 기여 주체 (런타임 config)
- **핵심 구현(PR #6422~#6469)은 팀/타인 주도로 보임** [팀]. **본인 기여**: `runtime-config-dev` 플러그인이 `local.json`을 무조건 우선하던 버그 수정(#6614), Batch 미설치 다이얼로그 install URL 일관성(#9279), **linker의 dotenv override 문제 해결/복원**(NX 프로젝트 그래프 초기화 시 `.env.dev` 선로드) 등 — *시스템의 엣지/버그·linker 적용 지점에 기여*. [git: 본인] [기여확인: 본인 핵심 기여 범위 더 확인]

---

## B. 격리환경 — 특정 커밋 시점 클라이언트+서버 재현

### 1. 의사결정 흔적
- **목표**: 런타임 config 기반이 생기면서, **원하는 커밋 시점으로 Docker를 띄워 그 당시 클라이언트+서버 환경을 그대로 재현**하는 격리 환경 구축 → E2E 신호 신뢰성 확보. [code: docker/stack/setup.sh] [기억]
- **RFC-003 결정**: 격리 E2E 잡당 셋업이 ~32분(대부분 stateful 서비스 매 잡 부팅). → **stateful(mongo/redis/minio/billtap) 1세트 공유 + JVM 서버만 잡당** 모델 채택. [code: docs/dev-environment/RFC-003-shared-stateful-stack.md]
- **RFC-002**: localhost-first 개발환경(격리 isolated 레인 + dev-domain proxy 호환 레인). [code: docs/dev-environment/RFC-002-localhost-dev-and-fixtures.md]

### 2. 측정 가능한 변화
- **격리 E2E 셋업 시간 32분 → 약 3~5분** (RFC-003 공유 stateful 모델). [code: RFC-003]
- 잡당 격리는 인스턴스 공유 + **namespace prefix**로: Redis `SPRING_SESSION_REDIS_NAMESPACE`, MongoDB DB명 `dentbird_<run-id>`, MinIO bucket `dentbird-e2e-<run-id>`, Billtap `/runs/<runId>`. [code: RFC-003, docker-compose.app.yml]

### 3. 후속 영향 / 메커니즘 (코드)
- 단일 진입점 `docker/stack/setup.sh` (35+ 플래그): `--sha <commit>`로 커밋 지정(없으면 `git HEAD`), `--multiregion`(KR+US dual stack), `--run-id`(브랜치별 멀티 스택), `--frontend-dev <app>:<port>`(특정 앱만 host dev-server swap). [code: docker/stack/setup.sh]
- 아티팩트 자동 검색: S3 `_releases/{app}/{sha}.tar.gz`(FE 번들) + ECR `dev-${sha:0:7}` 태그(BE 이미지). NX affected로 커밋에 산출물 없으면 `resolve_nearest_sha()`로 최근접 검색. [code: setup.sh, docker-compose.s3.yml]
- NX 1급 진입점 `nx run dev-stack:up / reset / apply-account`. [code: tools/dev-stack/]
- 통합 Docker 구성(클라+서버 함께): `docker-compose.{yml, stateful, app, s3, backend-host, dev-persistent}.yml` 레이어. nginx→platform-server(8408)/api-gateway(8500). [code: DOCKER.md, DEVELOPMENT.md]
- mirrord: `.mirrord/*.json`으로 로컬 JVM ↔ 실제 dev EKS cluster DB/Redis 직결(트래픽 mirror). [code: .mirrord/, DEVELOPMENT.md]

### 4. 운영 패턴 / ★ 기여 주체 (격리재현)
- **본인 기여 다수** [본인]: self-hosted runner 포트 누수 컨테이너 정리, **격리 E2E 회귀 가드**(crown 케이스 썸네일 aurora, 임플란트 정합 fixture + viewer abutment), dev-stack에 **로컬 SQS 에뮬레이터(elasticmq) 추가**, crown 서버 오프로드 SQS 경로 + 로컬 E2E 런북, **격리 dev stack 첫 기동 단순화**(`--frontend-dev` 포트 생략 + `--serve` 자동기동). [git: 본인, 2026-04~06]
- vault에 본인 운영·학습 기록 풍부 [vault: projects/dentbird-solutions/]:
  - `learnings/2026-05-28-isolated-fix-verify-light-path.md` (worktree 대신 메인 fix branch + host.docker.internal proxy + nginx reload 경량 경로)
  - `learnings/2026-05-22-isolated-multi-worktree-stack-env-resolution.md`, `2026-06-02-isolated-multiregion-stateful-boot-incident.md`
  - `decisions/2026-05-22-isolated-stub-faithful-to-prod-fault-by-default.md` (격리 stub은 prod 장애를 기본 재현)
  - `2026-W23-e2e-daily-revival.md` (E2E daily workflow 복구 — 인프라/fixture/보고 3층위)

---

## 미해결 (출처 확인 필요)

- **런타임 config 본인 핵심 기여 범위**: 버그픽스/linker 적용 외에 설계·구현 기여가 있었는지 본인 확인. (현재 근거상 핵심 PR은 팀) [기여확인]
- **CI 빌드 시간 "3배" 정확 수치**: 실측값/출처. [측정필요]
- **격리환경 재현이 실제로 어떤 버그/상황에서 효과를 봤나**: 구체 사례 1~2개(면접 답변용). [기억]
- 이 격리환경이 09 카드(EC2 Claude 자동 E2E)의 실행 기반인지 연결 확인. [기억]

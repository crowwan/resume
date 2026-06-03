# _ 서비스/플랫폼 개요 — "우리 서비스를 설명할 수 있게" (면접 대비)

> 이 파일은 **개인 카드(이력서 bullet)용이 아닙니다.** 면접에서 *"회사 서비스/기술 스택을 설명해보세요"*에 답하기 위해, **본인이 직접 안 했어도** 알아야 할 팀/플랫폼 차원 기술을 모으는 카탈로그입니다. (사용자 요청 2026-06-03)
>
> **기여 주체 태그**: `[본인]` 본인 기여 / `[팀]` 팀·타인 주도(서비스 이해용) / `[발굴대기]` 아직 코드 미조사. 이력서 본문엔 `[본인]`만, 면접 "서비스 설명"엔 전체 활용.
> **발명 금지**: `[팀]`/`[발굴대기]` 항목을 본인 성과로 말하지 않는다. "팀에서 했고 나는 이렇게 이해한다"로.

---

## 1. 서비스 개요

- **DentBird** — 이마고웍스(ImagoWorks)의 AI 기반 치과 CAD/CAM SaaS, 글로벌 B2B. [기억]
- 제품군(도메인): cloud(cloud-desktop), crown, modeler, milling, batch 등. 초기엔 서비스별 도메인·관리팀 분리 → 통합 진행. [code: dentbird-solutions/apps/, 03-mfe 참조]
- 멀티테넌트(Dentium/Axsys/CI Medical 등 호스트 도메인) + 멀티리전(KR, US_EAST). [code: libs/runtime-config deriveUrls, .mirrord]

---

## 2. 플랫폼 아키텍처 (통합의 큰 그림)

- **dentbird-solutions 모노레포(NX + pnpm)**: 흩어진 앱·서버를 한 레포로 통합. [code]
- **공통 모듈 통합(MFE)**: setting/export/explorer/viewer를 iframe 런타임 통합 → 빌드타임 → 다시 iframe(same-origin `/cloud/module.html`). [→ findings/03-mfe] `[본인]`(초기 주도) + `[팀]`(최종 결과)
- **런타임 Config("Build Once, Deploy Everywhere")**: 빌드 1회 → env(`config.js`)만 교체해 dev/qa/prod 배포. [→ findings/01-solutions] `[팀]` 핵심 + `[본인]` 일부
- **격리환경 커밋 재현**: `setup.sh --sha <commit>`로 특정 커밋의 클라+서버를 Docker로 재현(E2E 신뢰성). [→ findings/01-solutions] `[본인]` 다수
- **통합 배포 전략**: 불필요 배포를 줄이려 여러 앱·서버 통합. [발굴대기 — 무엇을 왜 통합했는지]

---

## 3. 성능·최적화 (팀 차원 — 서비스 설명 시 알아야 할 것)

- **iwtk(VTK) → Three.js 전환**: 모듈의 iwtk 의존 제거 → cloud-desktop 배포 번들 **119MB→51MB(-57%)**, iwtk WASM 리소스 68MB 제거(초기 로딩 단축). vendor lock-in 해소. [Confluence page/2282094594] `[팀]` (본인 카드는 08로 별도)
- **prefetch로 viewer 여는 속도 최적화**: viewer 오픈 속도를 prefetch로 개선. [발굴대기 — 어떤 리소스를 어떻게 prefetch, 효과] `[팀]`
- **빌드 시간 최적화**: 빌드 시간 단축 작업(런타임config의 빌드 단일화 외에도). [발굴대기 — Rspack 전환·NX 캐시·affected 등 추정] `[팀]`
  - 단서: cloud-desktop **Webpack→Rspack** 전환(TS7056 회피+속도). [vault: decisions/cloud-desktop-rspack-migration.md] `[팀]`

---

## 4. 관측성 (Observability)

- **Datadog 본격화**: 가시성 향상(RUM/로그/소스맵). [발굴대기 — 적용 범위·무엇을 잡았나] `[기억/본인?]`
- **Grafana Faro**: dev 환경 collector(`FARO_COLLECTOR_URL`). [code: deploy/config/dev.json] `[팀]`

---

## 5. 인증·보안

- **서버 JWT 토큰 → Session Cookie 전환**: 인증 방식 전환. [발굴대기 — 동기(보안/SSR?), 본인 BE 기여 범위] `[본인?]`
- **파일 접근 권한 암복호화**: 파일 접근 시 암복호화 기능 추가(진행 중). AEAD envelope 관련 vault 단서. [vault: learnings/2026-05-27-aead-envelope-*] [발굴대기] `[본인?]`

---

## 6. 테스트·자동화

- **E2E 본격 도입**: Playwright 기반. [→ findings 09 예정] `[본인]`
- **EC2 Claude 자동 E2E**(과거): 크론 pull→변경 분석→관련 E2E 실행→Teams 보고. 현재 미사용. [→ findings 09 예정] `[본인]`

---

## 추가 발굴 대기 목록 (다음 라운드)

1. prefetch viewer 최적화 (코드·효과) `[팀]`
2. 빌드 시간 최적화 (Rspack/NX 캐시/affected) `[팀]`
3. 앱·서버 통합으로 배포 감소 (무엇을 통합, 효과) `[팀/본인?]`
4. Datadog 관측성 적용 범위 `[본인?]`
5. JWT→session cookie 전환 (BE) `[본인?]`
6. 파일 암복호화(AEAD) `[본인?]`

# Electron 빌드·배포·자동업데이트 인프라 (self-hosted 러너·코드서명·blue-green) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · (조회일 표시 생략)
> 기존 `findings/05-batch.md`에 이미 있는 사실은 **중복 적재 제외**. 여기엔 그 위에 **새로/보강된** 사실만.
> 출처 태그 범례: `[git: <repo>@<sha7>]` · `[PR: <repo> #<num>]`(Azure DevOps Merged PR) · `[jira: KEY]` · `[confluence: pageId]` · `[vault: path]` · `[기억/측정필요]`. ★ = 기존 findings에 없던 NEW. ⚠️ = 카드 경계/팀 구분 주의.

---

## 본인 기여 식별 요약

- **본인(jwkim) 단독·주도로 확인된 인프라 영역**: Electron 데스크톱(batch-native / legacy-batch / linker-desktop) 빌드 파이프라인, Windows 코드서명(self-hosted 물리 빌드머신·에이전트 풀 운영 포함), macOS notarization, blue-green 배포 CLI/env/헤더, Azure→GHA self-hosted 이관, e2e detect 크론(EC2) 배포·운영. 전부 Jira reporter=assignee=jwkim 교차검증 또는 PR/커밋 author=jwkim.
- ⚠️ **본인 기여 아님 — [팀-hckim]**: Confluence `2221735944` "Cloud Desktop CI/빌드 최적화 종합 보고서"는 **cloud-desktop(웹 SPA, Vite/Rspack)** 대상이고 작성자·PR merge author = Hyeoncheol Kim. 이 보고서의 수치(6분→1.5분, Build 60→4.5초, pnpm 193→2초 등)는 **본 Electron 카드에 절대 인용 금지**. 완전히 다른 트랙(웹 CI vs 데스크톱 빌드·서명·배포).
- ⚠️ vault `decisions/cloud-desktop-rspack-migration.md`(webpack→rspack)에 김진완이 참여자로 명시되나 **cloud-desktop 웹 빌드 영역** → 본 카드 아님(Case2 모듈 마이그레이션 카드 소속).

---

## 1. 의사결정 흔적

- ★ **Windows 코드서명 — self-hosted 물리 빌드머신·에이전트 풀 운영까지 본인 범위**: Self-hosted Windows 빌드 PC에 두 번째 에이전트 `SigningAgent02`를 직접 설치, 동일 Agent Pool `electron-build-pool-for-windows`에 등록 → batch-native·linker-desktop 파이프라인 **동시 실행** 가능하게 구성 `[jira: D1-2732, 2026-02-19→02-20 완료]`. = CI yml 설정뿐 아니라 **물리 빌드머신·에이전트 풀 운영(인프라 엔지니어링)**까지 단독 담당. 같은 티켓에서 linker-desktop의 버전 하드코딩(`maker.options.{env}.json`)을 `VERSION` 환경변수 오버라이드로 전환(batch-native는 이미 `process.env.VERSION || projectJson.version` 지원, linker만 보강) `[jira: D1-2732]`. (커밋 출처 `[PR: dentbird-solutions #31037 D1-2732]`와 매칭.)
- ★ **"계획서 선행 → 실행" 운영 습관 (코드서명 파이프라인)**: `docs/plans/2026-01-29-self-hosted-code-signing-pipeline.md` 작성 후 실행 — linker-desktop에 고정이름 아티팩트 복사 스텝 추가 + **batch-native 파이프라인을 linker 기반으로 신규 생성** `[jira: D1-2361, 2026-02-02→02-03 완료]` (커밋 출처 `[PR: dentbird-solutions #30182 D1-2361]`와 매칭).
- ★ **source map/코드서명 버그도 계획서 기반 처리 + 당일 4시간 해결**: `docs/plans/2026-03-11-batch-mac-sourcemap-codesign-fix.md` 작성. 문제 = QA Batch Mac 빌드 **#108898** PKG notarization Invalid → macOS 설치 불가. 해결 = 독립 스크립트로 분리돼 있던 source map 주입을 electron-builder `afterPack` 훅으로 옮겨 **서명 전 주입** `[jira: D1-3608, 2026-03-11 당일 09:23→13:34 (~4h)]` (커밋 `[PR: dentbird-solutions #32030 D1-3608]`와 매칭 — 빌드ID·계획서 경로가 NEW).
- ★ **Electron 자동업데이트 방식 = 도입 전 구조 연구(R&D)부터 본인 주도**: Research Task `Electron app installer custom 및 Update 방식 구조 확인` `[jira: DB-12, 2023-12-29→2024-01-15]`. 기존 findings는 도입·세팅 커밋만 있었고 **"도입 전 조사·구조 결정 단계부터 단독"이라는 출처가 NEW**.
  - 그 위 실제 세팅(기존 findings의 DB-* 커밋과 reporter/assignee 교차검증 완료): electron-builder 초기 config `[jira: DB-103]`, 배포·업데이트 환경 설정 `[jira: DB-120/DB-121/DB-237]`, Windows code sign `[jira: DB-453]`, crash Reporter(datadog) `[jira: DB-521]`.

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

> 기존 findings의 "20분+ → 6분 내외"는 `[기억/측정필요]`로 남아 있음. 아래는 **PR 본문에 실제로 적힌 정량**으로, 기존 findings에 없던 수치 백본.

- ★ legacy-batch **macOS 빌드시간 56% 단축** (Katsukichi 스텝 19.2분→8.4분; 전체 macOS 파이프라인 33~39분→17~24분) — SWC 전환 + ForkTsChecker 비활성 `[PR: dentbird-solutions #32092 D1-3608]`. 그 전 단계 lint/typecheck 중복제거로 약 30% 추가 `[PR: dentbird-solutions #32061 D1-3608]`.
- ★ macOS artifact **757MB → 334MB (-56%)**, Upload **114초 → 47초 (-59%)** `[PR: dentbird-solutions #7884 Phase-3.6]`.
- ★ BrokerServer console queue **약 99% 감소** `[PR: dentbird-solutions #7884 Phase 4.2]`.

## 3. 후속 영향

- ★ **macOS 아키텍처 3연속 번복 (Universal→arm64→Intel x64)** `[PR: dentbird-solutions #32194 → #32199/#32200 → #32612]`. 앱 용량 약 2배 트레이드오프 때문에 11일 만에 되돌림. = "도입→운영 한계 체감→되돌림" 성장/판단 서사.
- ★ **blue-green 메커니즘 구체 구현 + 앱 간 포팅**: `--preview` CLI 플래그 / `DEPLOY_PREVIEW` env + `x-imago-deploy-preview` 헤더 자동주입을 구현하고, batch(P004) → legacy-batch(P002)로 **포팅** `[PR: dentbird-solutions #32192, #32567]`. (기존 findings는 blue-green "고민"만 있었고, 실제 구현 메커니즘·헤더명·포팅이 NEW.)
- ★ **2-Stage Build/Sign 병렬화 실험 → 의도적 롤백**: Cloud Agent 메모리 부족으로 `maxParallel:1` 강제 → 병렬화 효과 없어 롤백 `[PR: dentbird-solutions #30216]`. = 성장 서사(실험→측정→철회).
- ★ **e2e detect 크론을 EC2에 직접 배포·운영 (인접 인프라 슬라이스)**: `e2e detect --dry-run` 로컬 테스트 + Teams Webhook 전송 테스트 + **EC2 크론잡(`*/10 * * * *`, 10분 주기)** + 로그 로테이션 설정 `[jira: D1-993, 2025-12-01 완료]` (계획서 D1-987, 선행 스킬문서 D1-992). = 빌드·배포 외에 **스케줄러/크론·EC2 운영**까지 본인 인프라 범위. ⚠️ Case4(AI 변경감지) 카드와 경계 — 여기서는 "배포/운영 인프라" 측면만.

## 4. 운영 패턴

- ★ **self-hosted runner `/tmp` tmpfs 32G 100% full → 전앱 빌드 실패 사건 (디스크 위생 규약 확립)**: build-machine.local에서 release tarball을 `/tmp` + S3 + 공유캐시(`/mnt/build-data`)에 3중 저장하는데 공유캐시만 5세대 정리하고 **`/tmp` 원본 rm 누락** → 4일 만에 738개 26G 누적(8앱 × 약 76M/커밋) → `gzip: No space left on device`. 즉시 회수 `sudo find /tmp -maxdepth 1 -name '*.tar.gz' -mmin +5 -delete`(100%→19%, 26G 회수), 근본 fix는 `build-client.yml` tarball 스텝 끝에 `rm -f "$TARBALL"` 추가 `[vault: learnings/2026-06-05-build-runner-tmp-tarball-accumulation.md] [PR: dentbird-solutions #10857] [vault: log.md L118]`. 진단 anti-pattern = `/`·`/mnt/build-data`만 보면 "여유" 오판, `/tmp`가 별도 tmpfs(RAM 32G)임을 놓침. time-cost 약 35min, verified-on 2026-06-05.
  - ⚠️ 이 호스트는 **e2e·web 등 8앱 공유 빌드 호스트** 맥락이라, 빌드시간/디스크 수치를 "Electron 데스크톱 빌드만의 수치"로 인용하면 과대. **self-hosted runner 운영(디스크 위생·정리 규약) 측면 사실로만** 사용할 것.
- ★ **계획서 기반 일하기가 패턴으로 반복됨**: code-signing(2026-01-29), sourcemap-codesign-fix(2026-03-11), e2e detect 배포(D1-987) 등 인프라 작업마다 `docs/plans/*.md` 선행 작성 후 실행 `[jira: D1-2361, D1-3608, D1-987]`.

## ★ 기존 findings/이력서에 없던 새 증거

1. ★ **Windows 코드서명 self-hosted 물리 빌드머신 + 에이전트(`SigningAgent02`) 직접 설치·풀 운영** — CI 설정을 넘어 빌드 인프라 자체를 단독 운영 `[jira: D1-2732]`.
2. ★ **legacy-batch macOS 빌드 56% 단축 + artifact 757MB→334MB(-56%) + upload 114→47초(-59%)** — 출처에 적힌 실측 수치(기존 findings의 "6분"은 `[측정필요]`였음) `[PR: #32092, #7884]`.
3. ★ **macOS 아키텍처 Universal→arm64→Intel 3연속 번복(11일)** — 면접용 판단/번복 서사 `[PR: #32194→#32199/#32200→#32612]`.
4. ★ **blue-green 실제 구현 메커니즘**(`--preview` / `DEPLOY_PREVIEW` / `x-imago-deploy-preview` 헤더) + batch→legacy-batch 포팅 `[PR: #32192, #32567]`.
5. ★ **2-Stage Build/Sign 병렬화 실험→의도적 롤백**(Cloud Agent 메모리, maxParallel:1) `[PR: #30216]`.
6. ★ **self-hosted runner `/tmp` tmpfs full 장애 → 정리 규약 확립** — 인프라 운영·트러블슈팅 증거 `[vault/PR #10857]`.
7. ★ **e2e detect 크론을 EC2에 직접 배포(10분 주기)·운영** — 크론/EC2 운영 슬라이스 `[jira: D1-993]`.
8. ★ **Electron 자동업데이트 = R&D 조사 단계부터 단독**(`[jira: DB-12]`)이라는 출처.
9. ★ **인프라 작업의 "계획서 선행" 운영 습관**이 복수 티켓으로 입증 `[jira: D1-2361, D1-3608, D1-987]`.

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- "Azure 20분+ → self-hosted 약 6분"의 "6분" 정확값 — CI 로그 미확인 `[기억/측정필요]`. (단 §2의 56%/-56%/-59% PR 본문 수치는 확보.)
- batch-client v0.0.1대 초기 태그 존재 여부(로컬 태그엔 1.0.0부터) `[측정필요]`.
- blue-green green 환경 **자동검증 보류** 트레이드오프 — Jira/vault에 결정 노트 없음 → **인터뷰 보강 대상** `[측정필요]` (기존 findings §6에 정성 정리는 있음).
- ⚠️ **카드 경계 최종 정리**: 코드서명·자동업데이트·blue-green은 Linker/Batch 양 Electron 앱 공통 주제 → Linker 카드(02)와 사실 분리·중복 조율 必. e2e detect 크론(D1-993)은 Case4(AI 변경감지)와 경계.
- ⚠️ §4 build-machine 장애의 빌드시간/디스크 수치는 8앱 공유 호스트 맥락 → Electron 단독 수치로 환산 금지 (운영 규약 사실로만).

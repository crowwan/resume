# _GAP-REPORT — 전 영역 발굴 통합 갭 리포트

> 입력: 12개 영역 매니페스트(`collected/01~12`) + S3/전역 Jira(`collected/13`).
> 기준: 현재 `resume.md` / `career-description.md` 와 대조한 **새 증거·정정·확인 필요**.
> 안전장치: 수치는 출처에 적힌 값만. 추정·반올림·창작 0. 본인 기여 = git author=jwkim / Jira assignee=currentUser / vault 본인작성으로 교차검증된 것만 "본인", 그 외 [팀]/[기여확인].
>
> 작성일 2026-06-05 · 다음 단계 = 4번(확인 질문) 인터뷰 입력

---

## 1. 지금 이력서에 없는 가장 강한 새 증거 TOP 15

> 메모 형식: **[붙일 카드 / 포지셔닝 축]** + 한 줄 이유.

1. **모노레포 통합 = Git Subtree Split 이관 (본인 owner)**
   cloud 앱 2개 + libs 6개를 별도 레포에서 메인 NX 모노레포로 Git Subtree Split 이관 + 네임스페이스 리네임 + `git subtree pull` 지속 동기화. 현재 resume는 "모노레포 통합 주도"만 추상적으로 적음 — **구체 기법(Subtree Split·지속 동기화)이 없음.**
   `[git: dentbird-cloud-client-monorepo, code: docs/migration/*]` → **[카드1 Solutions / 구조축]**

2. **★정정확정: 웹 Build-Once / CI -90% / runtime-config 문서·소유권은 본인이 아니라 김현철(Hyeoncheol Kim)**
   Confluence creator=currentUser 전수조회 결과 본인 작성 인프라 문서 0건, 6개 핵심 문서 전부 Hyeoncheol author. 현재 resume/career의 "환경변수 런타임 주입 전환(팀 공동)"·CI 단축 수치는 **본인 비중 재조정 필요.** 본인 코드 기여는 linker dotenv override / NX cache VERSION / promote-fast html sync / cloud env 폴백 한정으로 보임.
   `[confluence: 2284322927/2284322880/2221735944 author=Hyeoncheol]` → **[카드1 / 정정 — 4번 질문 직결]**

3. **i18n 스크립트 중앙화 리팩토링 (본인 자작 Confluence 문서)**
   중복 89줄·package.json 14개 산재 스크립트·분산 NX 타겟을 `scripts/i18n/` 중앙화로 정리. **본인이 직접 쓴 Confluence 문서가 존재**(인프라 영역에서 드문 본인 owner 증거).
   `[confluence: 1700888768 author=Jinwan, 2025-10-10]` → **[카드1 / 효율축]**

4. **시각회귀 도입 WHY가 데이터로 존재 (자작 metric)**
   QA 회귀 보드 14일 103건(CRWN 86·DEN 17) 근원분석에서 "시각회귀 어설션=0 사각지대" 진단. Playwright `maxDiffPixelRatio 10%`가 못 잡는 mesh 회귀를 pngjs mean‖Δ‖ helper(`compareMeshRender`)로 **RED/GREEN 실증(GREEN mean 0.0 vs RED 2.2865 FAIL)**. 현 resume엔 "AI 변경 감지"만 있고 **시각회귀 자작 metric이 전무.**
   `[git: fc3ed17, PR #9517 DEN-4539]` → **[카드1 또는 신규 "3D 품질 자동화" 카드 / 판단·효율축]**

5. **격리 daily 만성 red 4-root 진단 + 본인 2개 PR 근본수정**
   `#10759`(동시성 — prefetch-frontends로 SHARED_CACHE 1회 warm) + `#10783`(billtap run-path, billtap 404 ~28건→0·passed 5→14). "격리 재현 토대"를 넘어 **만성 실패를 진단·근본수정한 운영 서사**가 새로 확보됨.
   `[git: dffd8e5, c4a11a7]` → **[카드1 / 품질·판단축]**

6. **3D 색/조명 정합을 gamma 우회(0.29)가 아니라 SRGB 정공법으로 본인이 재설계**
   findings 08의 gamma 0.29는 **본인이 폐기한 중간단계.** GENERIC UV draco 정공법으로 main-thread WASM 540줄 폐기 포함 ~1,420줄 제거. 현 resume는 "렌더링 정합·회귀 수정 담당(현재 진행)"만 — **정공법 재설계·대규모 제거가 안 보임.**
   `[git: e905f2e3d20 PR#8484 / 2a62abadf66 PR#9432]` → **[카드4 MFE-3D 또는 신규 3D 카드 / 구조·판단축]**

7. **3앱 공유 Three.js mesh visual regression CI 토대 본인 구축**
   swiftshader로 CI/local GPU 차이 흡수, 12 metric baseline, 회귀 시 +57~+139% / 0.89~5.29 delta로 정량 차단. **크로스앱 회귀 가드 인프라**는 현 이력서에 없음.
   `[git: 5b718d81ade / fc3ed17eaea PR #9517/#9655]` → **[카드4 또는 신규 3D 카드 / 효율·품질축]**

8. **VTK 썸네일 Three.js 통합 "안 하기로 한 결정"을 데이터로 정당화한 ADR**
   VTK 306파일·iwtk 649파일 소비 실측으로 "번들 감소 0" 증명 → 책임경계 공존으로 결론. **'안 한 결정'을 실측으로 정당화한 판단 서사** = 포지셔닝 "판단" 축의 강한 탄알.
   `[vault: decisions/2026-06-05-vtk-thumbnail-three-migration-deferred.md]` → **[카드4 / 판단축]**

9. **Windows 코드서명 = self-hosted 빌드머신에 에이전트(SigningAgent02) 직접 설치·풀 등록·운영**
   CI 설정을 넘어 **물리 빌드머신 + 에이전트 풀(electron-build-pool-for-windows) 단독 운영.** 현 career는 "self-hosted 빌드머신" 수준 — 에이전트 풀 운영 디테일이 없음.
   `[jira: D1-2732]` → **[카드2/3 Electron 인프라 / 범위축]**

10. **legacy-batch macOS 빌드 PR 본문 실측 수치 (이력서의 '20분→6분 체감'을 실측값으로 교체 가능)**
    Katsukichi 19.2분→8.4분, 전체 macOS 33~39분→17~24분(-56%), artifact 757MB→334MB(-56%), Upload 114초→47초(-59%). **현 resume의 "체감" 수치를 PR 기재 실측으로 승격 가능.** (단 'Azure 20분→6분'의 6분만 미확정)
    `[PR: #32092 D1-3608 / #7884 Phase-3.6]` → **[카드2/3 / 효율축]**

11. **별도 결제 팝업 앱 imago-payment-client 본인 구현 (부모↔팝업 2-앱 postMessage origin 격리)**
    SUB-331(add-seats)/SUB-387(business-error). 현 resume는 "구독 워크플로우 전체 구현"만 — **독립 결제 팝업 앱 + origin 격리 구조라는 아키텍처 디테일이 없음.**
    `[git: imago-payment-client@7de159e PR#76 / @b67f912 PR#79]` → **[카드5 Account / 구조축]**

12. **EB→Datadog 관측 표준 정렬 종합 계획서의 author = 본인 (★기존 [팀] 오기 정정)**
    `_service-overview.md §4`가 [팀]으로 오기한 영역이 실제 본인 owner. v1~v7 의사결정 진화 + 5 PR 분해 + EB↔Datadog 책임분리가 전부 본인 작성. 3-layer EB 아키텍처(Root→Section→Feature)를 D1-4763 epic으로 본인 확립 + 5앱 통합. **현 이력서에 ErrorBoundary는 Account 카드의 한 줄뿐 — 관측 표준 정렬 종합 계획이 전무.**
    단서: obs.*/error.code 어휘 정의는 hckim, 본인은 EB lib을 그 어휘에 align.
    `[confluence: 2364440747 author=Jinwan Kim / jira: D1-4763]` → **[신규 "관측성/EB" 카드 또는 카드5 보강 / 구조·품질축]**

13. **자기가 만든 Taxonomy 7분류를 v5에서 스스로 폐기한 자기-비판 성장 서사**
    Phase 5a에서 만든 Taxonomy 7분류·errorCode 자산을 v5에서 Datadog 자동 error.type/source/handling + session.id로 대체하며 **스스로 폐기.** 발명 위험 0의 "자기 계획 비판적 축소" = 미들급 판단 어필.
    `[confluence: 2364440747 갱신이력]` → **[관측성 카드 / 판단·지속성축]**

14. **Linker 감지 채널 부채 = "데이터 채널 vs 감지 채널" 2채널 분리 프레이밍 (본인)**
    custom-protocol이 데이터 전달은 풀었으나 **양방향성을 포기한 트레이드오프가 감지 채널 부채로 귀결.** 감지 채널 6안 정리·평가 후 백엔드 session-claim 폴링을 '검토 후 비선택'. 현 resume의 Custom Protocol 채택 서사에 **"무엇을 포기했나"가 빠져 있음** — 이 프레이밍이 결정→후과→재설계 검토 깊이를 보여줌.
    `[vault: cam-export-protocol-and-lna.md 본인작성]` → **[카드2 Linker / 판단·지속성축]**

15. **i18n 타입세이프 시스템 단독 설계의 코드 메커니즘·규모 확정 (랜딩)**
    `declare module 'i18next' + CustomTypeOptions` 확장으로 컴파일타임에 없는 키 차단, 영어 단일 소스→534줄 타입 자동생성, 34개 파일 적용, 2024-06 단독. v3 윈도우 author **jwkim 383 vs 2위 Kim Uhjin 24(약 16배, 약 90%)** — 현 career '361 vs 8'보다 강한 단독 주도 증거.
    `[git: @8075dce05~de091a574 / shortlog]` → **[카드7 랜딩 / 범위·구조축]**

> **차점 후보(16~)**: batch single-flight 토큰 갱신 인터셉터(`tokenRefreshPromiseRef`, DB-565) · 딥링크 dev/prod 스킴 분리(DB-63) · K8s→EC2 재전환 비용근거(EKS $150→EC2 $30/월) · console-client 커스텀 CDN 폴백 플러그인(remoteFallback 원작) · FSD lint 룰 직접 작성 · D1-2838 imago-theme 롤백 전담 · vendor OAuth interceptor priority guard(WV-002) · Export 실패 가시성 ADR(RUM 124건 collapse, DEN-4571).

---

## 2. 새로 확보된 정량 지표 전체 (출처·측정방법 — 발명 0 검증)

> ✅=본인 기여 / ⚠️=[팀] 또는 본인 효과 아님(인용 시 귀속 명시) / 🔸=실행시간·코드상수(제품 성과 아님, 과장 금지)

### 카드1 — Solutions 인프라 / 모노레포 / 빌드
| 지표 | 값 | 출처 / 측정 | 귀속 |
|---|---|---|---|
| NX 빌드 태스크 감소 | 11개→9개 | `[jira: D1-701]` nx graph 타겟 수 | ✅ |
| i18n 중복 스크립트 | upload 89줄, package.json i18n 스크립트 14개 산재 | `[confluence: 1700888768]` 라인/스크립트 카운트 | ✅ |
| 웹 클라 빌드 중앙값 | 345~523s→40~60s (-87~91%), p95 1,100~1,600s→70~108s (-93%), 6주 | `[confluence: 2284322880]` GHA CI 빌드 로그 집계 | ⚠️ [팀-Hyeoncheol] |
| promote 시간 | ~120s(full rebuild)→~11s | `[confluence: 2284322927]` CI 단계 시간 | ⚠️ [팀-Hyeoncheol] |
| cloud-desktop CI 전체 | ~6분→~1.5분(-75%), pnpm install 193s→2s, build 56s→4.5s | `[confluence: 2221735944]` CI 로그 | ⚠️ [팀-Hyeoncheol] (★Electron 트랙과 혼동 금지) |
| Platform Server CI | 2m10s~3m50s→45~52s | `[confluence: 2290221058]` CI 로그 | ⚠️ [팀-Hyeoncheol] |

### 카드 — 테스트 자동화 / 격리 / 3D 품질
| 지표 | 값 | 출처 / 측정 | 귀속 |
|---|---|---|---|
| Billing 매트릭스 자동화율 | active 45건 중 42건 = 93.3% (pr-smoke 21/daily 15/nightly 15) | `[confluence: 2369061053 §11]` scenarios.ts 집계, 2026-05-12 | ⚠️ hckim 문서값, 본인 슬라이스 [기여확인] |
| 시각회귀 기대 차단율 | QA 86건 중 23건 ≈27% | `[confluence: 2356150364 §7]` QA 보드 수동 분류 | ⚠️ 팀 문서값 |
| QA 회귀 보드 14일 | 103건(CRWN 86·DEN 17) | `[confluence: 2356150364]` 보드 집계 | ⚠️ 팀 문서값(본인 진단 근거로 인용) |
| 시각회귀 RED/GREEN 실증 | baseline md5 cc4f10d2/101,698B vs RED 7554d944/63,671B, diff 23,496/726,960=3.232%; compareMeshRender(thr 2.0) GREEN 0.0000 vs RED 2.2865 FAIL | `[git: fc3ed17]` pngjs mean‖Δ‖ 자작 | ✅ |
| 이벤트 기반 wait 가속 | thumbnail-visual 전체 120s→37.7s(-68%), test2 단독 ~120s→19.7s (前 waitForTimeout 60s+30s) | `[vault, commit 1466bbc1363]` Playwright 실행시간 | ✅ 🔸(테스트 실행시간) |
| billtap 해소 | 404/missing ~28건→0건, passed 5→14 | `[git: c4a11a7]` billing E2E 실측 | ✅ |
| K8s→EC2 비용 | EKS ~$150/월 → EC2 ~$30/월(1/5) | `[git: 84dea72]` 비용 추정(문서 명시) | ✅ |
| cross-app 회귀 delta | setupDentalLighting 누락 시 pair +57~+139%, batch outputColorSpace 회귀 시 절대값 0.89~5.29(TOL 0.5 초과), 정상 variance≤0.5 2회 PASS | `[git: 5b718d81ade PR#9655]` playwright visual + pngjs | ✅ |
| visual regression baseline | 4 case × 3 pair = 12 metric, ROI 640×720 center crop (canvas cloud 932×780/crown 1600×955/batch 726×791) | `[git: 5b718d81ade]` playwright, viewport 1600×1000 | ✅ |
| GENERIC UV 정공법 제거 | ~1,420줄 (draco-generic 540줄 등) | `[git: 2a62abadf66 PR#9432]` git diff 라인수 | ✅ |
| 레거시 TC 격리 편입 | 누계 ~141 TC (per-PR 명시값 합산, 실제 총량 더 큼) | `[git: 다수 PR]` 커밋 subject 합산(불완전) | ✅ 🔸(합산 불완전, '약' 표기 필수) |
| 격리 편입 PR 단위 | DEN-4539 본인 22커밋, DEN-3706 본인 17커밋(squash 전) | `[git: grep count]` | ✅ 🔸(squash 전 — 이력서엔 PR 단위 권장) |
| 기타 테스트 수 | cloud-mesh-io design specular 84/84, batch-web 유닛 2635/2670, DEN-3706 156 spec/1129 tests, CRWN-3294 cloud-mesh-io 159 | 각 vitest/spec runner | ✅ 🔸(가드 통과 수치, 성과 아님) |
| platform-server healthcheck | ~100초 | `[confluence: 2286158153]` | ⚠️ (전체 cold-start 미측정) |
| z-fighting depth 정량 | VTK gl_FragDepth 0.000016=1/65536=16bit, iwtk polygonOffset(1,-50)≈-0.0008 | `[vault: 2026-06-04]` 소스 메커니즘 분석 | ✅ |
| 마이그레이션 잠복결함 age | 도입 2022-07-05 + 2023-12-07 = 2~3년 묵은 결함 2026-05 surface | `[vault: 2026-05-27]` git blame | ✅(진단) |
| 이 영역 본인 Jira | 31건 전수 status=완료 | `[jira: assignee=currentUser]` | ✅ |

### 카드2 — Linker / Electron 인프라
| 지표 | 값 | 출처 / 측정 | 귀속 |
|---|---|---|---|
| 본인 'Export to SW'/Linker Jira | 최소 50건(JQL 한도), 2026-01-08~06-04, 월별 01=22/02=14/03=6/04=2/05=5/06=1 | `[jira: assignee=currentUser()]` JQL 자체추출 | ✅ (직접 해결 비율은 [기여확인]) |
| DEN-4583 | P1, 생성 2026-05-18 → 종료 2026-05-27 | `[jira: DEN-4583]` | ✅ |
| Linker 로컬 서버 | 포트 :55000, GET /api/health · POST /api/export | `[code: linker-desktop/src/server/server.ts]` | ✅ |
| 현 아키텍처 엔드포인트 | POST /v4/export-sessions · GET /v4/export-sessions/{id}/files | `[code, vault]` | ✅ |
| openProtocolWithFallback | 휴리스틱 회피용 10s timeout | `[code: useCamSWExportProtocol.tsx]` | ✅ |
| legacy-batch macOS 빌드 | Katsukichi 19.2분→8.4분, 전체 33~39분→17~24분(-56%) | `[PR: #32092 D1-3608]` PR 본문 | ✅ |
| lint/typecheck 중복제거 | 추가 ~30% 단축 | `[PR: #32061 D1-3608]` PR 본문 | ✅ |
| macOS artifact | 757MB→334MB(-56%) | `[PR: #7884 Phase-3.6]` PR 본문 | ✅ |
| macOS Upload | 114초→47초(-59%) | `[PR: #7884]` PR 본문 | ✅ |
| BrokerServer console queue | 약 99% 감소 | `[PR: #7884 Phase 4.2]` PR 본문 | ✅ |
| D1-3608 sourcemap/codesign | 당일 09:23→13:34 (~4h) 해결, 빌드 #108898 | `[jira: D1-3608]` Jira 타임스탬프 | ✅ |
| /tmp tmpfs 회수 | 100%→19%(26G), 738개 tarball 4일 누적(8앱×~76M/커밋), time-cost ~35min | `[vault: 2026-06-05, PR #10857]` | ✅ ⚠️(8앱 공유 호스트 — Electron 단독 수치 아님) |
| EC2 크론 주기 | */10 * * * * (10분) | `[jira: D1-993]` | ✅ |
| CAM SW 수 | 8(linker-app)→12(linker-desktop); career의 '16'은 코드 미확인 | `[code]` | 🔸[측정필요] career '16' 재확인 |

### 카드5 — Account / 결제
| 지표 | 값 | 출처 / 측정 | 귀속 |
|---|---|---|---|
| BO 구독 E2E 실행시간 | 1.8분→1.1분(-39%) describe.serial + Download 재사용 | `[git: 18725aaedcc PR#26389 / jira: D1-860]` | ✅ 🔸(테스트 실행시간, 제품 성과 아님) |
| Export 다운로드 횟수 | 5회→1회(-80%) | `[같은 PR]` E2E 내 동작 횟수 | ✅ 🔸 |
| 결제 폴링 한도 | base 3000ms · 최대 20회 (setInterval→재귀 setTimeout + cleanup) | `[git: 31fce352 PR#537 SUB-407]` 코드 상수 | ✅ 🔸(코드값) |
| 구독/시트/빌링 도메인 테스트 파일 | 33개(현 src, dealership 3 포함; 기존 04는 32개) | `[code]` | ✅ 🔸(시점·기준 차이 — 정성 권장) |
| 딜러십 운영버그 assignee | 36건 | `[jira]` | ✅ |

### 관측성 / 교차관심사
| 지표 | 값 | 출처 / 측정 | 귀속 |
|---|---|---|---|
| EB 테스트 | Phase 4a 42건(기존9+신규33), 5PR 통합시 13 files/164 tests | `[git: 50afb5cfe0e 등]` | ✅ |
| GA4 빌드 차단 hotfix | 5개 client Deploy 차단(modeler/batch-web/cloud-mobile/crown/milling) 복구 | `[git: 6ecb99ece80 PR#9573]` | ✅ |
| GA4 mock drift | spec mock 12개 inline 산재, spec 추가/수정 월 52건(3개월 156건) | `[git: ca1d708d36b PR#9686]` (범위 재확인) | ✅ 🔸([기여확인] 범위) |
| Export RUM collapse | 124건이 단일 에러 메시지로 collapse (2026-05-12~20, QA env) | `[vault: 2026-05-20]` | ✅ |
| ⚠️ 인증/세션 (본인 효과 아님) | 세션만료/비활동 QA = 인증 50건 중 34%(~17건) | `[confluence: 2251161860]` author Hyeoncheol | ⚠️ ★본인 fix 효과로 인용 금지 |
| ⚠️ AEAD KMS Decrypt | per-page 92회→1~3회, 760ms 감소 | `[confluence: 2412740622]` | ⚠️ BE팀, 본인 작업 아님 |
| ⚠️ JWT→Redis | Redis<1ms vs MongoDB 수~수십ms, API 4~7단계→3단계, 로그인 12→7단계 | `[confluence: 2177794115]` | ⚠️ BE팀 |

### 본인 정량 (인증/세션 FE 소비측 — 줄 감소뿐)
| 지표 | 값 | 출처 | 귀속 |
|---|---|---|---|
| overrideSessionHandlers | explorer/setting/viewer 각 14줄→1줄, export 20줄→1줄 (4모듈 통합) | `[git: 26b29a8db9e]` diff 라인 | ✅ |
| CookieUtil.getDomain() | 18줄→6줄(67%) | `[git: 70ab6689d8c]` diff | ✅ |
| 세션만료 i18n | 11개 언어 | `[git: 03421a7df47]` | ✅ |
| DEN-4583 fixture | plain/masked crown.drc.gz 각 8800 bytes, gzip 1f8b vs XOR e074 | `[git: 92d99f17c8e PR 9670]` | ✅(XOR 마스킹 검증, AEAD 아님) |

### 랜딩 / 디자인시스템 / Batch v1
| 지표 | 값 | 출처 | 귀속 |
|---|---|---|---|
| v3 윈도우 author | jwkim 383 vs Kim Uhjin 24 vs Daeil Jung 9 vs David Ryu 5 (2023-09-19~12-31) | `[git: shortlog --all]` | ✅ (약 90%, 2위 16배) |
| client 레포 전체 | jwkim 510커밋 = 6위 | `[git: shortlog]` | ✅ |
| 타입세이프 i18n | resources.d.ts 534줄 자동생성, i18next.d.ts 9줄, 34개 파일 | `[git/code]` | ✅ |
| v3.0.x 릴리스 태그 | 47개 (v3.0.0 2023-10-29 → v3.0.34 2026-01-22, 2년+) | `[git: tag]` | ✅(지속성) |
| server LANDING-134 | controller +28, 2파일, jwkim 5커밋, PR #3 (Fastify route + Mongoose updateMany) | `[git: @9223d46]` | ✅(풀스택 슬라이스) |
| 랜딩 Jira | jwkim assignee 47건 전부 종료 | `[jira: LANDING-99~153]` | ✅ |
| DEN-548 DatePicker | lib +8/-3, 소비처 3앱 각 +2, E2E 회귀 신규 121라인, WorkspaceLogs PO +25 | `[git: d0c5e48 #6511]` --stat | ✅ |
| D1-2838 imago-theme 롤백 (실측) | DS 내부 Snackbar/DialogueTitle 각 +2/-2 = 2파일 4라인 | `[git: 27066a7 PR#31251]` | ✅(실측) |
| D1-2838 (티켓 계획값, '약') | cloud-desktop ~79파일, cloud-mobile ~35파일, Typography 47 variant, 안전파일 ~109 자동복원+5 수동, ~3h | `[jira: D1-2838]` 계획값 | ✅ 🔸('약/티켓 기준' 유지) |
| Batch v1 author | 본인 347커밋/1위 (2위 yjka 126, 3위 ajin 110), 태그 25개, 2023-12-29~2025-01-24 | `[git: shortlog]` | ✅(기존 05에 존재) |
| Batch remote 타임아웃 | 15분(1000*60*15) Promise.race, 재시도 MAX 3 | `[code: crownModuleManager.js]` | ✅ 🔸(코드상수) |
| FSD/MF 모노레포 | 본인 88커밋(2025-06-18~07-09, ~3주), 3b71c3a Initial commit | `[git]` | ✅ |
| iframe 모듈 릴리스 태그 | 49개(explorer 12/export 18/setting 7/viewer 12) | `[git tag]` | ✅ |
| MF remote | 4종 운영 / console-client 런타임 소비 2종(dev·qa·development 3환경) | `[code: console-client/.env.*]` | ✅(prod 미확인) |

### S3 / 전역 Jira (규모증거 — 본인 단독 아님, [규모증거] 태그)
| 지표 | 값 | 출처 |
|---|---|---|
| Electron 인스톨러 | Batch prod 1.1.85 Win 97.9MiB/mac 127.8MiB, Linker 1.0.5 78.1MiB + experimental 2.0.x 별도채널 | `[aws s3 download-prod]` |
| 채널 분리 | dev 1.3GiB/qa 1.2GiB/prod 1.1GiB + 채널별 update 매니페스트(dev/qa/release.yml) | `[aws s3]` |
| E2E 자동화 규모 | 56,415 객체/19.1GiB, EKS 멀티리전(apne2 10,026런+use1 6,693런; apne2는 18일 윈도=보존분, 누적 아님) | `[aws s3 automation-test-results]` |
| 품질 fixture | e2e-fixtures .drc baseline 200개/56.8MiB, Storybook VR baseline 88장 | `[aws s3 dentbird-artifacts]` |
| MFE 카탈로그 | 비-preview 배포앱 24개 + module-federation 독립 prefix + 앱별 _preview 채널 | `[aws s3 clients-prod-web-assets 11.8GiB]` |
| 전역 Jira 도메인 | DEN 34/CRWN 11; 컴포넌트 Viewer 12/Export 12/My Designs 7/New Case 5; 타입 버그 45/50 | `[jira updated 상위 50]` |

---

## 3. 본인 기여 정정 사항 (기존 이력서 과대/과소 — 팀↔본인)

### 🔴 과대 표현 → 축소·재귀속 필요
1. **런타임 환경변수 분리 "팀 공동 설계"** (resume L44 / career L46): Confluence 6문서 전부 hckim author, 웹 파이프라인 전부 김현철. 본인 코드 기여는 linker dotenv override / NX cache VERSION / promote-fast html sync / cloud env 폴백 한정. **"공동 설계"는 과대 — '본인은 코드 기여 일부 + Electron 트랙' 톤으로 정정.** (질문 4·5 직결)
2. **CI 빌드 "3배 감축"/"20분→6분"** : 화려한 -90%/CI 6분→1.5분 수치는 전부 hckim **웹 트랙**. 본인 정량은 Electron 트랙(legacy-batch macOS -56% 등) 한정. **웹 수치 인용 금지.** resume L16/career L18의 "Electron 빌드 20분대→6분"의 6분은 미확정 → PR 실측(-56%/-59%)으로 대체 권장.
3. **iwtk→Three.js 전환 "본인 담당"** (career L107): 신규 cloud-mesh-io 구축 + ViewerDialog 전환 골격(번들 -57%/-68MB)은 [팀-hckim]. 본인은 그 위 레이어(렌더 정합·SRGB 재설계·회귀 인프라·SoT 통합). **번들 감소 정량은 본인 것 아님.** 단 정공법 재설계·1,420줄 제거·visual regression CI는 본인 → **과소된 본인 기여를 끌어올려야 함(아래 4번).**
4. **인증/세션 "ErrorBoundary로 이관"** : AEAD/JWT→Redis 코어는 BE팀(본인 코어 커밋 0건). 본인은 FE 소비측 안정화(overrideSessionHandlers·IdleTracker·Export 쿠키 포워딩)뿐. **Confluence 34%/92→1-3회/760ms/Redis<1ms 수치 인용 금지.**
5. **디자인 시스템 "공통 컴포넌트 기여"**: MUI 마이그레이션 전략 9문서는 hckim, imago-theme 도입도 본인 아님(본인은 롤백·복원만). 본인 기여 = 단일 컴포넌트 정밀 수정 + 릴리스/롤백 운영. **"디자인 토큰 시스템 설계"로 읽히면 과대.**
6. **GA4** (career 미기재지만 향후 주의): 택소노미/전환 attribution 설계는 hckim(2026, 현 모노레포). 본인은 깨진 빌드 hotfix + mock drift만. **GA4 설계 본인 기여로 쓰면 면접 리스크.**

### 🟢 과소 표현 → 끌어올려야 할 본인 기여
7. **EB→Datadog 관측 표준 정렬**: `_service-overview §4`가 [팀] 오기. 실제 종합 계획서 author=jwkim, 3-layer EB·5앱 통합·D1-4763 epic 본인. **현 이력서 한 줄(Account ErrorBoundary)을 신규 카드/축으로 승격 가능.** (단 어휘 정의는 hckim, 본인은 align)
8. **3D 렌더 정합 = 단순 "수정 담당"이 아니라 정공법 재설계 + 회귀 인프라 구축**: SRGB 정공법(gamma 0.29 폐기), 1,420줄 제거, 3앱 visual regression CI, VTK 통합 보류 ADR. **현 표현("렌더링 정합·회귀 수정 담당")은 본인 깊이를 심하게 과소평가.**
9. **시각회귀 자작 metric**: pngjs mean‖Δ‖ helper로 Playwright 한계를 넘은 자작 도구 — 현 이력서에 전무.
10. **랜딩 단독 주도**: '361 vs 8'보다 강한 **383 vs 24(약 90%, 16배)** + 2년+ 단일 라인(태그 47개) + 풀스택 슬라이스. career의 단독성 근거를 강화.
11. **Linker 운영 장기성**: 본인 assignee 50건+ / 2026-06까지 지속 / Datadog 연동(D1-1824). "2025.12 이후 Solutions 통합"으로 끝난 듯 보이나 **2026년에도 단독 운영 지속.**

### ⚪ 경계·중복 조율 (어느 카드로 귀속할지 사용자 결정)
- 코드서명/blue-green/자동업데이트: batch·linker 공통 → 현 career는 카드3로 통합. PR 귀속(어느 앱) 사용자 결정.
- e2e detect 크론(D1-993): Electron 인프라 카드 vs Case4(AI 변경감지) 카드 — 경계 모호.
- ErrorBoundary 표준화: MFE 카드 vs 관측성 카드 vs Account 카드.
- Batch VTK 뷰어(DB-580)는 v1 세대 / VTK→three 마이그레이션·통합보류 ADR은 P004 후속 세대 — 세대 경계 분리 필요.

---

## 4. 사용자 확인 필요 질문 통합 리스트 (다음 인터뷰 입력)

> 발명 방지의 핵심. 사용자만 답할 수 있는 사실/수치/기여. 우선순위順.

### 🔴 1순위 — 기여 주체 (이력서 정확성 직결, 면접 리스크)
Q1. **런타임 config 본인 역할**: 기존 findings의 '본인 핵심 설계 참여'는 git+Confluence상 웹 파이프라인 전부 김현철로 확인됨. 본인은 **'설계 논의 참여'였나 '코드 기여'였나?** (코드 기여는 linker dotenv/NX cache VERSION/promote-fast html sync/cloud env 폴백 한정으로 보임) `[기여확인]`
Q2. **'CI 3배 감축' 수치 출처**: 이게 김현철 웹 파이프라인 -90%인가, 본인 Electron 트랙인가? 본인 정량은 Electron 한정인지 확인. `[측정필요]`
Q3. **cloud-desktop Webpack→Rspack 전환** 본인 단독인가 팀 공동인가? (vault: '김진완 + in-repo team', 본인 확정 슬라이스는 getPublicPath util뿐) `[기여확인]`
Q4. **모노레포 통합 자체의 정량 효과**(CI 빌드시간/생산성)가 어느 문서에도 없음 — 체감 효과라도 있나? `[측정필요]`
Q5. **imago-payment-client 결제 앱** 초기 스캐폴딩·전체 결제 플로우 설계가 본인 단독인가 팀 공동인가? (SUB-331/387 커밋은 본인 확정) `[기여확인]`
Q6. **BO 구독 E2E '대량 작성'**에 다른 작성자 기여가 섞였나(공동 작성 여부)? `[기여확인]`
Q7. **Billing 자동화율 93.3%**는 hckim 문서값 — 본인이 자동화한 시나리오 수 vs 팀 합산 분리? `[기여확인]`
Q8. **'Export to SW' 50건 중** jwkim이 직접 코드 수정·해결한 비율 vs 단순 트리아지/이관 비율? `[기여확인]`
Q9. **console-client MF 본인↔Adam 경계**: 런타임/RuntimeModule/CDN 폴백=본인 원작, 개별 remote 연동(#21365/#22207)=Adam — 본문 분리 표기 맞나? `[기여확인]`
Q10. **EB 5앱 통합 중 crown-client '부분'**의 본인 적용 범위 어디까지인가? `[기여확인]`
Q11. **WV-002 interceptor 진단 / CRWN-3489 콜백 재설계**가 본인 단독 진단·설계인가 팀 리뷰 동반인가? `[기여확인]`
Q12. **DEN-4194 '5일 작업'** 중 본인 단독 비중 vs 협업 비중? `[기억]`

### 🟡 2순위 — 효과/사례 (정성 보강, 면접 답변용)
Q13. **AI 변경감지(e2e detect)가 2025-12 운영 중 실제로 잡은 회귀 구체 사례 1~2개** (git/Teams/Jira 미발견 — 기억 필요). `[기억]`
Q14. **격리환경 커밋 재현이 실제로 잡은 버그/상황 1~2개.** `[기억]`
Q15. **DEN-4466/4503(window.blur 휴리스틱 오염) 최종 결론**: 6안 중 '③ 비-trapping UX 통일'이 사용자 선호 후보로만 기록 — 실제 머지/적용·PR번호·결과는? (성과로 쓰려면 확정 필요) `[측정필요]`
Q16. **회귀 가드 유닛('claim 없으면 launched=false')** 실제 테스트 작성·머지됐나? (미작성이면 성과 X) `[측정필요]`
Q17. **자기가 만든 Taxonomy 7분류를 v5에서 왜 스스로 폐기했나** (Datadog 자동 분류로 99% 대체 판단 — HOW/지속성 축). `[기억]`
Q18. **D1-1824 Datadog 연동(Linker)** 정성 임팩트: 무엇을 관측 가능하게 만들었나? `[기억]`
Q19. **Account 제품 측 정량 지표** (결제 버그 감소율·구독 전환 성공률·커버리지%) — D1-860의 -39%/-80%는 테스트 실행시간이라 제품 성과로 쓰면 과장. 댈 제품 지표가 있나? `[측정필요]`
Q20. **디자인 시스템(DEN-548) 수정으로 사용자/QA가 체감한 개선**(포커스 인디케이터 클레임 빈도 등)? `[기억]`
Q21. **EB 마이그레이션 전후 실제 triage 시간 단축·미캐치 감소** 정량? (문서의 'Session Replay 2분 완주'는 목표값) `[측정필요]`
Q22. **vendor OAuth interceptor guard(WV-002) 이후 401 stale-token 재발 0** 여부? (unit test PASS만 확인) `[측정필요]`
Q23. **SUB-407 무한 폴링**이 실제로 일으킨 증상(브라우저 부하/배터리/서버 부하)? `[기억]`
Q24. **15분 타임아웃·재시도 3회·WASM 메모리 모니터**가 실제 운영에서 잡은 사례(타임아웃 빈도·OOM)? `[측정필요/인터뷰]`
Q25. **crown window 프로세스 분리**의 정량 효과(메인 UI 프리징 개선)? `[측정필요/인터뷰]`

### 🟢 3순위 — 범위/표기/정정 확인
Q26. **CAM SW 정확 수치**: career '16' vs 발굴 12(linker-desktop) vs 8(linker-app). 현재 실제 지원 수? `[측정필요]`
Q27. **DEN-4539/3706 커밋 수(22/17)는 squash 전** — 이력서에 커밋 수 대신 PR 단위(#8484, #10301/#10620)로 표기할까? `[측정필요]`
Q28. **PR additions/deletions가 Azure DevOps 머지라 gh 검증 불가** — -172줄/-1420줄/테스트 수를 커밋·vault 기재값으로 이력서에 그대로 쓸까? `[측정필요]`
Q29. **테스트 파일 33개 vs 기존 32개** — 시점/집계 차이인지 실제 증가인지. 모호 수치 대신 정성 기술 권장. `[측정필요]`
Q30. **blue-green green 검증 자동화 보류**의 진짜 결정 근거 (Jira/vault에 결정 노트 없음). `[측정필요]`
Q31. **'Azure 20분→self-hosted 6분'의 6분 정확값** (CI 실행 로그 미확인). PR 본문 -56% 등은 확보. `[기억/측정필요]`
Q32. **iframe(고객용 4모듈) vs MF(콘솔 remote)를 같은 주에 분기한 실제 판단 근거**? `[기억]`
Q33. **FSD 도입 동기 + 베어→접두형 레이어 네이밍 전환 계기**? `[기억]`
Q34. **react-query v3 + @tanstack v4 공존**이 본인 마이그레이션 결정인가 레거시 혼재인가? `[기여확인]`
Q35. **AutoScrolling(무한 횡스크롤)** 구현 방식(CSS vs JS, 라이브러리 vs 직접)·근거? `[기억]`
Q36. **D1-2838 롤백 이후 '토큰 확정→재마이그레이션'으로 실제 이어졌나** (지속성 서사)? `[기억/측정필요]`
Q37. **MF remote prod 운영 여부/규모** (console-client .env.prod 미존재, dev·qa만 확인)? `[측정필요]`
Q38. **ErrorBoundary 표준화를 MFE 카드에 넣을지 관측성 별도 카드로 둘지** 본인 판단. `[기여 모호]`
Q39. **e2e detect 크론(D1-993)을 Electron 인프라 카드 vs Case4 카드** 어디에 귀속? `[경계]`
Q40. **이력서 격상 결정**: 인증/세션 코어(AEAD/JWT→Redis) 격상 비권장 재확인 — 'FE 세션·인증 안정화' 보조 bullet(IdleTracker 3단·vendor OAuth·Export 쿠키 포워딩) 채택 여부? `[사용자 결정]`

---

## 5. 수집 공백 (더 캐야 하는데 못 캔 영역)

1. **Azure DevOps PR 본문 미조회**: MFE(console-client #20407/#21365/#22207 등)·3D(#8484 이후 additions/deletions)가 Azure DevOps 머지라 `gh`로 본문·diff 검증 불가. → Azure DevOps API/MCP 필요. 현재 라인수·테스트 수는 커밋 본문/vault 기재값 인용 수준.
2. **제품 측 임팩트 지표 전반 부재**: Account(결제 버그율·전환율), EB(triage 시간 단축·미캐치 감소), 시각회귀(실제 막은 prod 회귀 건수), AI detect(실제 잡은 회귀) — 전부 코드/테스트 지표만 있고 **제품 결과 지표가 없음.** 발명 금지 영역 → 인터뷰(Q13·14·19·21)로만 보강 가능.
3. **CI 실행 로그 직접 미확보**: 'Azure 6분'·모노레포 통합 생산성·cold-start 전체 소요 등 — PR 본문/Confluence 기재값은 있으나 raw CI 로그 집계 미수행.
4. **격리환경 본인↔hckim 슬라이스 미분리**: Confluence 6문서 전부 hckim 명의 → 격리 기술이슈 10건·dhub CLI·세션 직접주입·호스트 farm 중 본인 PR 슬라이스가 모호(Q 다수 [기여확인]). daily 진단만 root별로 분리됨(root C=본인 #10783, root B=hckim #10762).
5. **Linker 'Export to SW' 50건 내부 분류 미완**: JQL 한도(50)로 잘렸고, 직접 코드수정 vs 트리아지 비율 미분류(Q8). PR #6945 author 미확인.
6. **DEN-4466/4503·회귀 가드 유닛 머지 여부 미확정**: vault에 '후보'로만 존재 — 실제 머지·PR 번호·결과 미확인(Q15·16). 성과로 쓰기 전 필수 확인.
7. **vault 미존재 영역**: 랜딩(프로젝트가 vault 운영 이전 — 사실 0건), Batch v1 Jira description 본문 대부분 null → 티켓별 배경은 인터뷰 의존.
8. **MFE prod 운영 규모**: console-client .env.prod 미존재로 MF remote prod 가동 여부·규모 미확인(Q37).

---

*다음 단계: §4 1순위(Q1~Q12) → 2순위(Q13~Q25) 순으로 인터뷰. 답이 모이는 대로 각 카드 findings 반영 후 resume/career 재작성.*

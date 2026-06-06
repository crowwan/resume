# 교차 관심사 (Datadog 관측성·GA4·빌드최적화·Stripe) — 본인 기여 슬라이스 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · (조회일 표시 생략)
> 기존 `findings/_service-overview.md`(§3 빌드 / §4 관측성 / §7 Stripe / §8 GA4) 및 `findings/collected/04-electron-infra.md`(Electron 빌드·/tmp 장애)·`07-account.md`(Stripe DEN-4330)·`05-mfe.md`(D1-2811 iframe postMessage)에 **이미 있는 사실은 중복 적재 제외**. 여기엔 그 위에 **새로/보강된** 사실, 특히 **error-boundary→Datadog 관측 표준 정렬(본인 핵심)** + 칼분리 정정만.
> 출처 태그 범례: `[git: repo@sha7]` · `[PR: repo#num]` · `[jira: KEY]` · `[confluence: pageId]` · `[vault: path]` · `[기억]` · `[측정필요]`. ★ = 기존 findings에 없던 NEW. ⚠️ = 기여 주체/카드 경계 주의.

---

## 본인 기여 식별 요약

- **본인(jwkim) 주도로 확인된 이 카드의 핵심 슬라이스 = error-boundary 라이브러리(`libs/error-boundary`)를 Datadog 관측 표준(§7/§9)에 정렬·구현.** EB→Datadog 종합 계획서(Confluence `2364440747`)의 **author = Jinwan Kim 본인** — v1~v7 의사결정 진화 + 5 PR 분해 + EB↔Datadog 책임분리 + Dogtap 검증법이 전부 본인 작성. = "EB→Datadog은 본인 영역"의 1급 primary 증거. `[confluence: 2364440747 author=Jinwan Kim]`
- ⚠️ **정정 (1단계 과대 진술 수정)**: `obs.domain`/`obs.event`/`obs.result`/`error.code`/`session.invalidation_reason` 등 **21개 공통 attribute 어휘를 *정의*한 건 hckim**(Datadog 3일 실행 계획 `2356150511`의 §7/§9 = source-of-truth, author hckim + backend gateway/platform + 4앱 RUM 표준화). **jwkim은 그 어휘에 EB lib을 *align*한 프론트 구현체** — 어휘 원천 정의자가 아님. `[confluence: 2356150511 author=hckim]`
- ⚠️ **본인 기여 아님 — 칼분리 재확정**: ① GA4 전환측정·Attribution Bridge·ecommerce·서버 Measurement Protocol·Stripe→GA4 purchase/refund = **hckim** (`2383052830` author, repo ds4/connect-monorepo). ② web client 빌드 90% 단축(Webpack→Vite→AzDO→GHA, shared node_modules) = **hckim/팀** (`2284322880` author) — 본인은 Electron/desktop GHA 트랙만(→ `04-electron-infra.md`). ③ RUM 데이터 분석(Crown SG 느림·Hub 호주 속도) = **hckim** (`2369683499`/`2360410349`).
- **본인 GA4 기여 = 구현 핵심 아닌 *빌드 차단 hotfix + mock 안정화*** (hckim 측정 코드가 깨뜨린 production build를 본인이 살림). Stripe는 표시버그/환경변수 fix(→ `07-account.md` DEN-4330에 이미 적재).

---

## 1. 의사결정 흔적

### error-boundary → Datadog 관측 표준 정렬 (본인 owner)
- ★ **EB→Datadog §7/§9 정렬을 v1→v7 7단계로 본인이 직접 의사결정 진화**(문서 갱신이력에 박제):
  - v2 "4앱 이미 §7/§9 적용 확인 → PR3 삭제"
  - v3 "taxonomy 잉여 평가 → deprecation" (자기 계획 비판적 축소)
  - v4 "EB가 obs 자동부여 안 하는 게 옳다 결정"
  - v5 "errorCode 자산 완전 제거 → Datadog 자동 session.id로 스크린샷 triage 전환"
  - v6 "logAsyncError 단일 진입점 + addObsError thin wrapper 통합"
  - v7 "Dogtap PR #8858 baseline 캡처 + PII scrub 범위 확장(error.stack 내부 query string)". `[confluence: 2364440747]`
- ★ **hckim과의 합의 근거 명시**: "이전 구현 내용을 현재 문서 방향으로 맞추는 식"(김현철 컨플루언스 코멘트 2026-05-08) → jwkim의 EB 자산을 hckim §7/§9 표준에 맞추는 방향 확정. `[confluence: 2364440747]`
- ★ **책임 분리 설계**: EB 자동 경로(throw/unhandledrejection)는 `origin`만 부여, 비즈니스 도메인 분류(`obs.domain`)는 **호출자가 addObsError로 명시**(EB 자동부여 X). 두 경로가 `logAsyncError` 단일 진입점으로 수렴. `[confluence: 2364440747 §3.3 / git: 71aa5bb9340]`
- ★ **batch-web가 5앱 중 §7/§9 미적용 유일 앱**이었음(나머지 cloud-desktop/cloud-mobile/account-client/crown-client는 hckim이 직접 적용) → jwkim이 batch-web RUM 전면 재구성(PR2)으로 **5앱 정렬 완성**. `[confluence: 2364440747 §2.1 매트릭스]`
- ★ **errorCode/taxonomy "잉여 자산" 제거 결정의 정량 근거**: lib 약 100~150줄 + 책임 1개 감소. `@context.fingerprint`/`@context.taxonomy` 운영 의존 grep 후 없으면 즉시 제거, 있으면 30일 transition. `[confluence: 2364440747 §1·§6]`
- ★ **GlobalErrorHandler report-only + fatal 예외 정책**(Phase 4a), **Datadog Error Taxonomy 7분류 beforeSend 태깅**(Phase 5a, 이후 v5에서 자산제거로 폐기), **RUM 비즈니스 컨텍스트 4필드 자동병합**(user.id[PII제외]/workspace_id/route/case_id). `[git: 50afb5cfe0e PR#6874 / c20597afe00 PR#7020]`
- ★ **"각 앱 추가 mount 0줄" + zero-dependency 컴포넌트(SessionInfoLabel/SessionBadge)** 설계. vault onboarding 정책: "RootFallback의 design-system/Hook/i18n 의존 **절대 금지** — 안전망이 자기 의존으로 죽으면 의미 없음". `[git: 9ce1d436c74 / vault: _onboarding/error-boundary-architecture.md §4]`
- ★ **3-layer EB 아키텍처(Root→Section→Feature) + report-only 비동기 원칙**을 jwkim이 **D1-4763 epic으로 확립**, **5앱 통합**(cloud-desktop/cloud-mobile/account-client/batch-web 완전 + crown-client 부분). 미도입 9앱(modeler/milling/linker-desktop/dealer-bo/crown-bo 등). `[vault: _onboarding/error-boundary-architecture.md §1·§3 / jira: D1-4763 "Error Boundary 개선 프로젝트 — Fault Tolerance 체계 구축" 완료]`
- ★ **Export 실패 가시성 전략 ADR (DEN-4571)** — "동일 UI가 모든 fail mode를 같은 메시지로 흡수 → root cause 추적 불가"를 본인이 ADR로 의사결정. 사용자 원래 가설(react-error-boundary 라이브러리, Option B)을 "async event handler/iframe 흐름엔 정공 아님"으로 **기각**하고 **Custom ExportPipelineError class + useShowErrorBoundary RUM 직렬화(Option C)** 채택. RUM 124건(2026-05-12~05-20, QA env, dentbird-cloud-desktop-client)이 `new Error('failedToExportMeshes')` 한 줄로 collapse되던 문제. `[vault: decisions/2026-05-20-export-error-visibility-strategy.md / git PR#9762, DEN-4571]`

### GA4 (본인 = 빌드 차단 hotfix + mock 안정화, 구현 핵심 아님)
- ★ **GA4 빌드 차단 hotfix**: hckim의 `feat(ga4): send subscription ecommerce conversions`(5833e51) 이후 `getGA4ClientId` closure narrowing이 production ci-build(rspack/tsc strict)에서 TS2722 → **modeler/batch-web/cloud-mobile/crown/milling 5개 client Deploy 전부 차단** → 본인이 지역변수 capture로 hotfix. `[git: 6ecb99ece80 PR#9573, 2acc13aef92]`
- ★ **GA4 mock schema drift 해소**: spec mock 12개가 inline으로 흩어짐 → `vi.importActual` 전역 setup으로 drift 0화. `[git: ca1d708d36b PR#9686]`

> Stripe/billing 본인 슬라이스(account 구독 갱신일 표시 `endDate ?? nextPaymentDate`, payment-client DEN-4330 Unknown error 환경변수 fix)는 `findings/collected/07-account.md` §DEN-4330 및 기존 `04-account.md`에 이미 적재 — 여기서 중복하지 않음. 핵심 = **Stripe 통합 자체는 본인 영역 아님, 표시/환경변수 버그fix만.** `[jira: DEN-4330 종료 / DEN-4517]`

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

### error-boundary 테스트 게이트 (본인 영역)
- ★ Phase 4a 시점 EB 테스트 **42건 통과(기존9+신규33)**. 5PR 통합 시점 **13 files / 164 tests pass**. Taxonomy 13건 + datadogContext 9건. `[git: 50afb5cfe0e / 71aa5bb9340 / c20597afe00]`
- ★ **Dogtap(격리 Datadog 수집기, PR#8858) 4 invariant 회귀가드** `apps/e2e/cloud/tests/isolated/dogtap/eb-observability-attributes.test.ts`(CLOUD-LOCAL-DOGTAP-04~07). 격리 emit baseline 시드: account 2 / batch 1 / crown 1 / cloud 0. `[vault: _onboarding/error-boundary-architecture.md §7 / confluence: 2364440747 §10 / pageId 2364310014 Before/After 검증]`

### GA4 (본인 기여 한정 — 측정값 = 영향 범위)
- ★ GA4 빌드 차단 hotfix 영향 범위 = **5개 client Deploy 차단**(modeler/batch-web/cloud-mobile/crown/milling). `[git: 6ecb99ece80 PR#9573]`
- ★ GA4 mock drift: spec mock **12개 inline** 산재, spec 추가/수정 빈도 **월 52건(최근 3개월 156건)**. `[git: ca1d708d36b PR#9686]`

### 빌드 (web client 트랙 — 팀/혼재, 서비스 맥락으로만 인용 가능)
- ⚠️ **[confluence 인용, NOT-본인 직접 정량] web client 빌드 중앙값 345~523초(6~9분)→40~60초(1분 이내), -87~-91%. p95 1,100~1,600초(18~27분)→70~108초(2분 이내), -93%.** 앱별: account 456→40s / cloud-desktop 429→59s / dealer-bo 345→44s / cloud-mobile 523→60s / payment-client 23s. 기여도 추정: CI 인프라(shared node_modules, pnpm 193s→2s) ~45% / GHA 전환 ~25% / Vite ~15% / IWTK 제거 ~10% / 기타 ~5%. **이 수치는 web client 트랙(author hckim) — jwkim 직접 기여 정량 아님. 인용 시 트랙 명시 필수.** `[confluence: 2284322880 author=hckim]`
- ★ **[confluence 인용] Datadog sourcemap upload = GHA 빌드에서 7초 blocking 스텝.** (1단계 git `842892badc9` "소스맵 업로드 service명 불일치 fix + batch-web 소스맵 추가"와 같은 영역 — 본인 fix는 service명 불일치/batch-web 추가 한정.) `[confluence: 2284322880]`

> self-hosted runner /tmp tmpfs 100% full 빌드 실패 사건(738개 26G, 100%→19% 회수, PR#10857)은 `findings/collected/04-electron-infra.md` §4에 이미 상세 적재 — 여기서 중복하지 않음. (이 카드의 "빌드 운영" 슬라이스로 교차 참조만.) `[vault: learnings/2026-06-05-build-runner-tmp-tarball-accumulation.md / git: PR#10857]`

### Datadog RUM 분석 (서비스 맥락 — hckim, 본인 무관)
- ⚠️ **[confluence 인용, NOT-본인] Crown Singapore 느림 분석**: 한 세션 평균 resource latency 11,277ms(5,000세션 중 4,997위, 상위 0.06% 최악). `/account/users/me` status 401:200 = 13,472:10,743(401이 56%) → 토큰 리프레시 루프. `POST /user/filebucket` 서버처리 자체 138~152초. RUM 1,212 이벤트 분석. `[confluence: 2369683499 author=hckim]`
- ⚠️ **[confluence 인용, NOT-본인] Hub 호주 업/다운로드 30일 분석**: 호주 32.6Mbps vs 미국 35.4Mbps(거의 동등, 회선BW 천장). 업로드 TTFB 99.95%+. hub-client timeout 180s→600s(CND-687, PR#30342). `[confluence: 2360410349 author=hckim]`

## 3. 후속 영향

- ★ **본인 EB lib가 5앱 공통 관측 인프라 + GA4 RUM mirror·CustomerIO 게이트의 기반.** jwkim이 EB에 emit하는 `obs.domain`/`obs.event`/`obs.result` dot-notation을 hckim/jhpark의 `track() RUM mirror`가 재사용. ⚠️ 단 **어휘 자체의 원천 정의는 hckim §7(2356150511)**, jwkim은 프론트 EB emit 구현체. `[git: cd432a885cd vs 71aa5bb9340 / confluence: 2356150511]`
- ★ **EB→Datadog 작업이 hckim 3일 계획의 Frontend RUM 트랙(§15 DD-6/DD-7)을 흡수·종결.** jwkim 문서가 "DD-6/DD-7 거의 완료(4앱 적용됨), 본 작업이 batch-web 마무리 + helper 통합 + Fallback UI 적용"으로 명시 = **백엔드 관측 계획의 프론트 절반을 jwkim이 실행 종결.** `[confluence: 2364440747 §8 / 2356150511 §15]`
- ★ **EB 아키텍처가 Export 가시성 ADR(DEN-4571)의 토대.** ADR이 "ExportDialog 외 Viewer/Share/Setting도 Custom Error class + RUM 직렬화로 일반화 가능, error visibility 전략을 patterns §10에 추가 검토"로 확장 경로 박음. `[vault: decisions/2026-05-20-export-error-visibility-strategy.md §장기]`
- ★ **provider-tree 의존성 추적 learning(CRWN-3489 콜백 격리)** — EB/RootFallback 경량 shell 분기 시 wrapper hook 의존성(IThemeProvider→useChannel→useMyInfo→useQuery) 미추적 시 런타임 throw, typecheck/unit 통과해도 안 잡힘. 코드리뷰 #10483 blocking으로 catch. `[vault: learnings/2026-05-29-provider-tree-wrapper-dependency-tracing.md]`
- ★ Datadog 소스맵 업로드 service명 불일치 fix + batch-web 소스맵 추가, (과거)account-client metaPixel 중복 useEffect 래핑 정리. `[git: 842892badc9 PR32173 / 7b95b52b181 PR31968 D1-3583]`

## 4. 운영 패턴

- ★ **회귀 가드 항상 동봉** (Dogtap 격리 가드, session.id 표시 가드 등). `[git: 71aa5bb / 50afb5c / vault Dogtap spec]`
- ★ **하위 호환 강박 + 점진 마이그레이션** (5앱 동시영향, Phase 4a→4b→4c, deprecate→삭제 예고, "동작 변화 0"). v3에서 PR5(통합) 폐기, v4에서 PR4 폐기처럼 **자기 계획을 비판적으로 축소**하는 패턴이 문서 갱신이력에 드러남. `[confluence: 2364440747 갱신이력]`
- ★ **"빌드 깨짐 = critical path" 선제 차단** (GA4 hotfix PR 본문 "nx typecheck 통과해도 production rspack build 차단 가능", "spec mock도 false GREEN→deploy 차단 패턴 잠재"). `[git: ca1d708d36b]`
- ★ **격리 환경 라이브 검증 선호**: Datadog 작업도 영구 evidence 안 남기고 Dogtap dashboard/API 한 줄로 즉석 조회. Playwright fixture 한계(storageState→인증 hook 우회→user attribute empty)를 정직하게 박고 "코드 실효성은 빌드/단위테스트 + qa 운영검증으로 확인"으로 분리. `[confluence: 2364440747 §10·§10.7]`
- ★ **추측 기반 hack 금지 정책화**: EB 정책 매트릭스에서 `isExplicitFatal` 자동 stub 금지(명시적 instanceof만), 메시지 매칭 분류기(isNetworkErrorMessage) 제거(false positive 위험), 분류별 toast 차등 제거(가치<복잡도). `[vault: _onboarding/error-boundary-architecture.md §4]`

## ★ 기존 findings/이력서에 없던 새 증거

1. ★ **EB→Datadog 관측 표준 정렬 종합 계획서(`2364440747`)의 author = 본인** — "EB→Datadog은 본인 영역"의 1급 primary 증거. 기존 `_service-overview.md` §4는 이걸 `[팀]`으로 오기. `[confluence: 2364440747 author=Jinwan Kim]`
2. ★ **칼분리 정정**: 21개 관측 attribute 어휘 *정의*는 hckim(`2356150511`), **본인은 EB lib을 그 어휘에 *align*한 프론트 구현체** — 1단계 "본인이 어휘 표준을 만들었다"는 과대였음을 명시 정정. `[confluence: 2356150511 author=hckim]`
3. ★ **v1→v7 자기 계획 비판적 축소 서사**: 자기가 Phase 5a에서 만든 Taxonomy 7분류·errorCode 자산을 v5에서 **스스로 폐기**(Datadog 자동 error.type/source/handling + session.id로 대체) — 발명 위험 0의 진짜 성장 트레이드오프. `[confluence: 2364440747 갱신이력]`
4. ★ **3-layer EB 아키텍처 D1-4763 epic 본인 확립 + 5앱 통합 + zero-dependency RootFallback 원칙.** `[jira: D1-4763 / vault: _onboarding/error-boundary-architecture.md]`
5. ★ **batch-web가 5앱 중 §7/§9 미적용 유일 앱 → 본인이 RUM 전면 재구성(PR2)으로 정렬 완성** = "백엔드 관측 계획의 프론트 절반을 본인이 종결(DD-6/DD-7 흡수)". `[confluence: 2364440747 §2.1·§8]`
6. ★ **Export 실패 가시성 ADR(DEN-4571)**: 사용자 가설(react-error-boundary, Option B) 기각 → Custom ExportPipelineError + useShowErrorBoundary RUM 직렬화(Option C). RUM 124건이 `new Error('failedToExportMeshes')` 한 줄로 collapse되던 문제. `[vault: decisions/2026-05-20-export-error-visibility-strategy.md / PR#9762]`
7. ★ **GA4 빌드 차단 hotfix(5 client Deploy 차단 살림) + mock drift 0화(월 52건 spec 빈도)** = GA4 영역에서 본인의 실제 기여 슬라이스(구현 핵심 아닌 빌드 게이트 방어). `[git: 6ecb99ece80 PR#9573 / ca1d708d36b PR#9686]`
8. ★ **Dogtap 격리 Datadog 수집기 4 invariant 회귀가드(PR#8858)** — 관측 코드에도 회귀가드 동봉하는 본인 패턴의 구체 증거. `[vault §7 / confluence: 2364440747 §10]`

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

1. [측정필요] **EB 마이그레이션 전후 실제 triage 시간 단축·에러 미캐치 감소 정량** — 커밋·문서엔 없음. 문서엔 성공지표로 "CS가 Session: xxxxxxxx 읽어 Session Replay 진입까지 2분 안에 완주(샘플 3건)"이 **목표**로만 박힘(실측 아님 — 이력서에 수치로 쓰면 발명).
2. [인터뷰 보강] ★ **가장 강한 HOW/지속성 축**: "자기가 Phase 5a에서 만든 Taxonomy 7분류를 왜 v5에서 스스로 폐기했는가"(Datadog 자동 error.type/source/handling로 99% 대체 가능 판단). 발명 위험 없는 진짜 성장서사 → 4축 HOW/지속성 보강 대상.
3. [기여확인] EB 5앱 통합 중 crown-client는 "부분"으로 기록됨 — 어디까지 본인 적용인지 사용자 확인.
4. [기여확인] GA4 mock drift fix의 "월 52건/3개월 156건"이 GA4 spec 한정인지 전체 spec 빈도인지 — PR 본문 재확인 필요(인용 시 범위 명시).
5. ⚠️ [정정 반영] 기존 `_service-overview.md` §4 "EB→Datadog 표준 정렬 `[팀]`" → **`[본인]`(계획서 author)으로 정정 필요**, 단 "어휘 정의는 hckim, 본인은 align"이라는 단서 동반.

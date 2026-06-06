# Micro Frontend — 공통 모듈 통합 (iframe·Module Federation·FSD) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 정량은 출처에 적힌 값만
> 기존 `findings/03-mfe.md`에 이미 있는 사실은 제외. 여기엔 **새/보강 사실만**.

---

## 본인 기여 식별 요약

- **확실한 본인 기여 (jwkim 원작/주도)**
  - 초기 iframe MFE(`module-monorepo`) 주도. 본인 커밋 = **29(default)/27(no-merge)/55(--all)** — 기존 findings의 "55"는 `--all` 기준. [git: module-monorepo --author jwkim]
  - **신규 레포 `dentbird-front-module-monorepo`(Azure `platform-module-federation-monorepo`)** Nx+Webpack+MF+FSD 전용 — 본인 **88커밋**, `3b71c3a Initial commit`이 본인 이니셜라이즈. 2025-06-18~07-09. [git]
  - console-client MF **런타임 도입·`RuntimeModule.ts`·커스텀 CDN 폴백 플러그인** 원작. PR #20407 "MF 설정 추가"(2025-06-17 본인 머지). [git: console-client@5e8dc29, c62fc97, 080951b]
  - FSD lint 룰·tsconfig path 강제·README 셋업 가이드 직접 작성(`3498493`, `138786b`, `e131586`). [code/git]
  - vault decision `module-migration.md` §호환 API 참여자에 **김진완 명시**. [vault]
  - **ErrorBoundary 표준화 작업이 "이 작업은 김진완님이 담당하기로 했습니다"로 본인에게 공식 배정**. [confluence: 2282782951]
  - 과거 `DMC-43 iframe 내 child window block 문제 해결`(2024-07-17, assignee=본인). [jira: DMC-43]
- **팀/타인 (본인 성과 아님 — 맥락 근거로만)**
  - ③단계 결과보고서 + Cloud Modules SDS 작성자 = **Hyeoncheol Kim**(D1팀). [confluence: 2282094594, 2411331634, 2282782951]
  - console-client 개별 remote 연동(tenant_change/feature_toggle, PR #21365·#22207) = **Adam Abror Iskandarov**. 현 `RuntimeModule.ts` blame이 전부 Adam인 건 본인 원작(c62fc97)을 타인이 reformat/이동한 결과. [git blame 대조]
  - 도메인 변경(모듈별 도메인 → cloud 하위)은 본인 작업 아님(기존 findings line 47 승계). [기억]
  - UPP 에픽 자체 assignee는 본인 무관 가능 — 텍스트 매칭으로 잡힘. 본인 기여는 MF remote 구현 쪽. [jira: UPP-124/125/126]

---

## 1. 의사결정 흔적

### iframe 4앱 폐기 이유가 vault에 명문화됨 (기존 findings는 "전환 중"만 언급) ★
- `decisions/module-migration.md` §Context에 **iframe 폐기 이유 5가지**:
  1. 다중 iframe ↔ 호스트 인증/세션 동기화 복잡
  2. postMessage 직렬화 비용 + 타입 안전성 부재
  3. 모듈 자체 Axios가 호스트 인증 인터셉터와 분기
  4. React Router 라우팅이 모달 UX에 부적합(URL 변경 부작용)
  5. 빌드 파이프라인 분기(모듈별 별도 build/deploy)
  [vault: decisions/module-migration.md §Context]
- **호환 API(`useEmbedModule()`)로 호출부 코드 무변경 전환** — `useModuleContext()` 자리에 그대로 들어가고 Provider만 `ModuleProvider→EmbedModuleProvider`로 교체, axios는 host 주입. (참여자에 **김진완 명시**) [vault: decisions/module-migration.md §호환 API]
- **통신 계약 전환 매트릭스**: postMessage `{message:'onSuccess'}` → React `onSuccess/onClose/onError` 콜백 / 라우터 → `mode` props 분기 / 모듈 내부 Axios → host 주입 / i18n·테마·DataDog 초기화 책임을 **host로 이전**. [vault: decisions/module-migration.md §핵심 변화]
- **검증 체크리스트에 "다중 인스턴스 동시 사용 race condition 가드"가 OPEN QUESTION으로 남음** — 같은 모듈 여러 개 동시 오픈 시 가드가 미해결 과제. [vault: decisions/module-migration.md §검증 / patterns.md §9]
- **export 모듈만 별도 트래킹된 이유 = IWTK WASM 의존**(`download-iwtk`/`link-peer-iwtk` NX 타겟이 host에 사전 구축돼야 동작) — 단순 컴포넌트 추출이 아니라 빌드/배포 셋업 동반 이동. [vault: decisions/export-module-migration.md]

### MF 영역(콘솔/BO) — 영역별 채택 + 런타임/폴백 설계 (본인) ★
- **MF는 "써보고 뺀 게 아니라 영역별 분리 채택"** — 콘솔/어드민(BO)은 MF, 고객용 공통모듈 4종(setting·export·explorer·viewer)은 iframe. (기존 findings line 28~29의 "MF 트레이드오프로 뺐다"를 정정·보강하는 결정 구조) [git/code]
- `dentbird-front-module-monorepo` = MF **remote 전용** 모노레포(host 없음, `remotes:` 선언 없음). remote 4종: `feature_toggle`(`./FeatureToggle`,`./bootstrap`) / `notification_bo`(`./Module`) / `notification_fo`(`./Module`) / `user_tenant_change`(`./UserTenantChange`,`./bootstrap`). [code: apps/*/module-federation.config.ts]
- MF 스택: `@module-federation/enhanced ^0.14.0`, `@nx/module-federation 21.2.0`, Nx 21.2.0, Webpack, React 18.3.1, MUI 5, TanStack Query 5, msw 2. [code: package.json]
- **shared 전략을 모듈별로 분기 결정**(트레이드오프 흔적): `feature_toggle` = react/MUI/react-query를 `{singleton:true, requiredVersion:false, eager:true}` 공유(중복 로딩 방지) vs `notification_bo` = `shared:()=>false` + `disableNxRuntimeLibraryControlPlugin:true`(의도적 완전 격리). [code: apps/{feature_toggle,notification_bo}/module-federation.config.ts]
- console-client = MF **consumer(host)**, 런타임 설정·CDN 폴백 = 본인 원작: `RuntimeModule.ts` 101줄 신규, `@module-federation/enhanced/runtime` `init()`로 remote 런타임 등록, `App.tsx`에서 `runtimeModule.initialize()`(`080951b`, 2025-07-09). [git: console-client@5e8dc29, c62fc97, 080951b]
- **커스텀 CDN 폴백 플러그인(`remoteFallback`)** 본인 설계: MF manifest fetch 실패 시(`errorLoadRemote`, `afterResolve`) CDN `serverUrl`로 `fetch(no-cache)` 재시도 — 운영 신뢰성 장치. [git: console-client@c62fc97]

### FSD 아키텍처 도입·룰화 (본인) ★
- FSD lint 룰 직접 작성: `3498493 chore: fsd lint 룰 적용`(2025-06-18). `import/no-internal-modules`로 레이어 import 방향 강제, `import/order`로 react→@app→@pages→@shared→@widgets→@features 자동 정렬. [code: eslint-fsd.config.mjs]
- FSD 레이어: `@app > @processes > @pages > @widgets > @features > @entities > @shared`, 세그먼트 `ui/model/lib/api/config/assets`, tsconfig path alias로 강제. [code: tsconfig.fsd.json]
- **네이밍 진화**(결정 변화 흔적): 초기 베어 레이어명 → 이후 알파벳 접두 정렬형(`a-app/b-pages/c-widgets/d-features/e-entities/f-shared`)으로 폴더 정렬=위계 가시화. [git: 49a5552, ad76b5e, 684cd94, 4722249]
- README에 "리모트 모듈 FSD 세팅" 표준 절차 문서화(eslint-fsd.config.mjs/tsconfig.fsd.json/CORS 헤더 템플릿) — 팀 재사용 셋업 가이드. [git: 138786b, e131586]
- remote 배포 = Azure Blob 버전 디렉토리 분리(prod = `NX_PUBLIC_APP_STORAGE_URL + version + '/'`)로 버전별 동시 서빙/롤백. DTS 플러그인으로 remote 타입을 consumer에 d.ts/zip 전달. [code: apps/notification_fo/webpack.config.ts]

---

## 2. 측정 가능한 변화  (출처에 적힌 수치만)

> 번들 정량(119→51MB / 17.61→15.16MB / iwtk 68MB)은 **기존 findings line 62~66에 이미 적재** — 재기재 안 함. 아래는 새로 확인된 정량/규모만.

- MF remote **4종 운영** / console-client 런타임 소비 remote **2종**(user_tenant_change, feature_toggle). 배포 엔드포인트 `mf-cdn.dev-dentbird.com`·`mf-cdn.qa-dentbird.com` 하위 `/tenant-change/remote/mf-manifest.json`·`/feature-toggle/remote/mf-manifest.json`(dev·qa·development **3환경**). [code: console-client/.env.*]
- iframe 모듈 릴리스 태그 **49개**(explorer 12 / export 18 / setting 7 / viewer 12). [git tag]
- FSD 모노레포 본인 **88커밋(약 3주, 2025-06-18~07-09)**. module-monorepo 본인 **29/27/55커밋**. [git log]
- Explorer dialog 상태 매트릭스 **11종**(importer / integration-medit / integration-shining3d / opener-redirect / opener-case / nested-export-open 등) — iframe 경계 계약 복잡도의 정량 증거. ⚠️ SDS 작성자 = Hyeoncheol Kim(본인 아님). [confluence: 2411331634]
- ③단계 마이그레이션 PR 목록 **19건 명시**: #6485(iframe 셸+postMessage 타입계약 Phase0) ~ #6503, Phase 0~5 + cloud-mesh-io 신규 구축. ⚠️ 보고서 작성자 = Hyeoncheol Kim(본인 아님). [confluence: 2282094594]

---

## 3. 후속 영향

- **iframe 공유 모듈이 팀 SDS·SPEC으로 제도화됨** ★: 본인이 초기 주도한 iframe MFE 패턴이 이후 D1팀의 정식 SPEC 문서로 체계화. SPEC 코드 `SPEC-MOD-EXPLORER-001 / SPEC-MOD-VIEWER-001 / SPEC-MOD-SETTING-001` — "Embed modules는 Cloud Desktop/Batch Web/Crown/Modeler host가 iframe/module boundary로 공유하는 dialog surface". = 한 사람 실험이 아니라 **여러 host가 운영 중 공유하는 정식 계약**. ⚠️ SPEC 저작 주체 = Hyeoncheol Kim(2026-05-27), 본인 아님 → "내가 깐 패턴이 팀 표준으로 정착" 서사로만. [confluence: 2411331634]
- **ErrorBoundary 표준화 작업이 본인에게 공식 배정** ★: `page/2282782951`(작성자 Hyeoncheol Kim, 2026-04-15) 말미 "이 작업은 김진완님이 담당하기로 했습니다". iframe/모듈 host 경계와 직결 — "다이얼로그(Viewer/Export/Share) 에러는 `sendToParent()`로 전달만 하고 다이얼로그 자체 ErrorBoundary 없음"을 cloud-desktop 문제점으로 적시 → iframe 모듈 경계 에러 격리가 본인 후속 과제. NEW. [confluence: 2282782951]
- FSD 룰·README 셋업 가이드를 본인이 정립 → 이후 팀의 tenant_change/feature_toggle remote가 같은 컨벤션으로 추가(타인 PR #21365/#22207). [code/git]
- MF CDN 폴백이 후속 운영 안정성 장치로 남음. [git: console-client@c62fc97]
- **타임라인 하이브리드 상태 단서** ★: vault decision(`module-migration.md`, 2026-05-20)은 "iframe→빌드타임 컴포넌트 전환 중(setting만 active)", 같은 영역 SDS(`2411331634`, 2026-05-27) + onboarding(2026-06-01)은 여전히 "iframe/module boundary 공유 dialog" → **embed-modules 흡수 후에도 비-cloud-desktop host(Crown/Modeler/Milling)는 iframe(`/cloud/module.html`) 유지**하는 하이브리드 상태. 기존 findings line 98 "iframe 회귀 시점" 의문의 답 단서. [vault: _onboarding/embed-modules-explorer-dialog-hosting.md / confluence 대조]

---

## 4. 운영 패턴

- **embed-modules 흡수 후 host별 호스팅 모델이 갈림** ★: cloud-desktop = inline(EmbedModuleProvider가 ExplorerDialog 직접 렌더, 같은 프로세스) / Crown·Modeler·Milling = **iframe**(`ModuleIframe`→`<iframe src=/cloud/module.html#/explorer>`, initPayload는 postMessage `{type:'init'}`). 공유 resolver 로직은 cloud-desktop이 서빙하는 `/cloud/module.html` 빌드에 들어가 **cloud-desktop 배포 시 Crown/Modeler/Milling 전부 즉시 반영**(각 host 재배포 불필요), consumer-specific 동작은 호출부 payload로 격리. = 본인 초기 iframe 호스팅 구조의 "회귀 폭발 반경"을 vault가 명문화. [vault: _onboarding/embed-modules-explorer-dialog-hosting.md §호스팅 모델]
- **iframe async onClose가 시각 결함 증폭원** ★: iframe guest의 onClose는 동기 setState가 아니라 부모로 postMessage(`sendToParent({type:'onClose'})` → host가 iframe 제거) — 왕복 async라 한 틱 gap 동안 iframe 콘텐츠가 보임(cloud-desktop inline은 React18 배치로 거의 안 보임). 닫기와 가시성 토글을 같은 액션에 섞으면 iframe host에서만 결함 발현(WV-005). = postMessage 경계의 운영 함정. [vault: embed-modules-explorer-dialog-hosting.md §iframe async onClose / bugs/WV-005]
- **Datadog에 iframe postMessage 런타임 에러가 prod 운영 중 관측** ★: `D1-2811 [Datadog Error] Failed to execute 'postMessage' on 'Window': Invalid target origin '' in a call to 'postMessage'`(2026-02-23). 에러 서비스 목록에 `setting-module-client` 등 iframe 모듈 service 포함 — iframe postMessage 통신이 prod 운영 중 실제 에러를 냈다는 증거. [jira: D1-2811]
- **과거 iframe child window 차단 이슈를 본인이 해결** ★: `DMC-43 iframe 내 child window 생성 시 block 되는 문제 해결`(Frontend Task, 종료, 2024-07-17, assignee=본인). iframe 모듈에서 새 창(OAuth 팝업 등) 생성이 막히는 문제 — iframe 호스팅 초기 운영 함정을 본인이 다룸. NEW(2024년 본인 iframe 작업 흔적). [jira: DMC-43]
- **Notification 기능이 정식 이니셔티브(UPP-124 Notification P001)로 등재**, BO/FO 분리(UPP-126 B.O / UPP-125 F.O, 2025-06-25) ★: MF 레포의 `notification_bo`/`notification_fo` remote 분리가 기획 이니셔티브와 1:1 대응. 런타임 진입점 `init({client})`/`inject(parentElementId,{language})`/`unmount()` 라이프사이클로 host가 DI 주입. ⚠️ UPP 에픽 assignee는 본인 무관 가능 — 본인 기여는 MF remote 구현 쪽. [jira: UPP-124/125/126]
- ViewerDialog는 `exactHost`('cloud'|'batch')에 따라 action 가용성·콜백 ownership 분기, stale 신호 `VIEWER_CASE_NOT_FOUND_SIGNAL`/`VIEWER_WORKSPACE_REMOVED_SIGNAL`을 host signal로 전달. ⚠️ SDS 작성자 Hyeoncheol Kim. [confluence: 2411331634]
- MF: shared 정책 모듈별 분기(eager 공유 vs 완전 격리), vite `onwarn` MF 경고 정리(2358199), msw 2.x로 remote 단독 개발/검증 환경 구축(3779b21, 8fd65fe). [git]

---

## ★ 기존 findings/이력서에 없던 새 증거

1. **신규 레포 `dentbird-front-module-monorepo`(Azure platform-module-federation-monorepo)** — Nx+Webpack+MF+FSD 전용, 본인 88커밋, `3b71c3a` 본인 이니셜라이즈. 기존 findings엔 이 레포 자체가 없음. [git]
2. **MF = 영역별 분리 채택(콘솔/BO=MF, 고객용 4모듈=iframe)** — 기존 findings는 "MF를 트레이드오프로 뺐다"였으나 실제는 영역 분리. 결정 구조 정정. [git/code]
3. **console-client 커스텀 CDN 폴백 플러그인(`remoteFallback`) 본인 원작** — 운영 신뢰성 장치. 기존 findings엔 `RuntimeModule.ts` 존재만, 폴백 설계는 없음. [git: c62fc97]
4. **FSD lint 룰·접두형 레이어 네이밍 진화·팀 README 셋업 가이드** 본인 정립 — 기존 findings에 FSD 없음. [code/git]
5. **shared 전략 모듈별 분기(eager 공유 vs 완전 격리)** — MF 운영 트레이드오프 판단 흔적. [code]
6. **iframe 폐기 이유 5가지 + 호환 API + race condition OPEN QUESTION** — vault에 명문화, 기존 findings엔 "전환 중"만. [vault]
7. **iframe 공유 모듈이 팀 SPEC(SPEC-MOD-*-001)으로 제도화** — "내 패턴이 팀 표준으로" 서사. [confluence: 2411331634]
8. **ErrorBoundary 표준화가 본인에게 공식 배정** — iframe 모듈 경계 에러 격리 후속 과제. [confluence: 2282782951]
9. **DMC-43(2024) iframe child window block 본인 해결** — 2024년 본인 iframe 운영 흔적. [jira: DMC-43]
10. **D1-2811 prod iframe postMessage Datadog 에러** — iframe 통신이 운영 중 실제 에러를 냈다는 객관 증거. [jira: D1-2811]
11. **host별 inline vs iframe 호스팅 + 배포 전파 반경(cloud-desktop 1회 배포 → 3 host 즉시 반영)** — iframe 호스팅 구조의 운영 이점. [vault: onboarding]

---

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- **MF remote prod 운영 여부/규모**: console-client `.env.prod` 미존재(local), dev·qa env만 확인. FSD 레포 `.env.production` host API가 `api.dev-dentbird.com`(=dev) 가리킴 → prod 운영 규모 [측정필요].
- **console-client MF 본인↔Adam 경계 확정**: 런타임/RuntimeModule/CDN 폴백=본인 원작(c62fc97), 개별 remote 연동(#21365/#22207)=Adam. 본문에 "MF 런타임 기반·폴백 설계는 본인, 개별 remote 연동은 동료"로 분리. [git blame 완료, 본인 확인 권장]
- **iframe(고객용 4모듈) vs MF(콘솔 remote) 분기 결정의 실제 이유**: 같은 주(2025-06-17~18) 시작 — "왜 같은 시기 한쪽 iframe·한쪽 MF"의 진짜 판단(소비처 설정 복잡도? 격리 요구? 배포 단위?)은 [기억]. vault decision의 iframe 폐기 이유 5가지가 단서.
- **FSD 도입 동기 / 베어→접두형 네이밍 전환 계기**는 [기억].
- **타임라인 하이브리드 상태 확정**: embed-modules 흡수 후에도 비-cloud-desktop host는 iframe 유지가 의도된 설계인지 [기억]. (기존 findings line 98 의문 해소 단서 확보)
- **ErrorBoundary 표준화(본인 배정, 2282782951)가 MFE 카드에 포함될지** — iframe 모듈 경계 에러 격리와 직결되나 별도 영역일 수 있음. 본인 판단 필요. [기여 모호]
- **cloud-mesh-io 트레이드오프**(pixel-perfect 불가, `VTK_COLOR_MATCHING_GAMMA=0.29`, vertex color 미세 색차)는 별도 카드(`08-iwtk-threejs.md`) 소관 — 본인 기여 범위 확인 포함. [confluence: 2282094594]
- Azure DevOps PR 본문(#20407 등)은 gh 조회 불가(Azure org). 추가 본문 필요 시 Azure DevOps API/MCP. [측정필요]

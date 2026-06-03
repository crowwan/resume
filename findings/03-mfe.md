# 03. MFE — 공통 모듈 통합 (iframe → 빌드타임 → iframe) — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: done (코드·git·vault·Confluence 발굴 + 4축 인터뷰 완료. ③단계 기여 주체 분리·일부 인프라 이유는 본문 작성 시 확정. iwtk→Three.js는 별도 카드로 분리 예정)
> 발굴 대상: `~/Works/workspace/module-monorepo`(초기), `~/Works/devops/dentbird-solutions/libs/embed-modules` + `apps/cloud-desktop`(이관 후), vault `projects/dentbird-solutions/decisions/`
>
> **핵심 맥락 — 3단계 진동 서사**: 공통 기능 4개(setting·export·explorer·viewer)의 통합 전략이 **① iframe 런타임 통합 → ② 빌드타임 통합(React 컴포넌트) → ③ 다시 iframe 런타임 통합**으로 바뀜. 각 전환이 **조직 개편·모노레포 통합·배포 전략 변화**와 맞물림. 기술 자체가 아니라 *"그때의 제약에 맞는 통합 전략"*을 선택한 판단 서사.

---

## 0. 배경 (사용자 설명 — 1차 source)

- 초기 서비스는 도메인 분리: `cloud / crown / modeler / milling.dentbird.com`, **서비스별 관리팀이 다름**. [기억]
- 공통 기능 4개:
  - **setting, export**: 모든 서비스 공통 dialog
  - **explorer**: cloud 제외 서비스에서 사용하는 dialog
  - **viewer**: cloud만 사용. mesh 파일 렌더링에 사내 vtk 라이브러리 **`iwtk`** 사용 — iwtk 관리 주체는 **크라운팀**. iwtk는 공통 라이브러리라 업데이트하면 viewer·export도 같이 업데이트해야 함. [기억] + [code: iwtk 의존 확인 ↓]
- **문제**: 이 4개 기능에 변경이 생기면 **각 서비스에 같은 수정을 반복하고 모든 서비스를 배포**해야 하는 번거로움. [기억]

---

## 1. 의사결정 흔적

### ① 초기: iframe 런타임 통합 선택 (module-monorepo)
- 4개 기능을 모듈화. **iframe을 통한 런타임 통합 전략** 채택. [기억] + [code: module-monorepo/apps/*]
- **탈락 대안 — 라이브러리(npm package) 배포**: 기존에 다른 컴포넌트를 모듈화해 라이브러리로 배포해봤으나, 매번 package 업데이트 → **각 서비스가 버전 올리고 다시 배포**해야 함 → *해결하려던 문제(반복 수정·배포)를 그대로 남김* → 탈락. [기억]
- **iframe 선택 근거**: 런타임 통합 방식 중 **가장 빠르고 복잡하지 않게 접근 가능**했던 것. [기억]
- **탈락 대안 — Module Federation** (실제 경험 기반 판단): 별도로 진행했던 **notification 기능에서 Module Federation을 도입해봤으나**, ① 초기 설정이 복잡하고 ② 모노레포가 아닌 경우 **소비처(consumer)에서도 추가 설정이 복잡** → 이 모듈 통합의 선택지에서 제외. [기억]
  - 단, **`console-client`(다른 서비스)에는 일부 Module Federation을 실제 적용했고 그 설정을 본인이 진행**. → MF를 "안 써봐서 뺀" 게 아니라 **써보고 트레이드오프로 뺀** 것. [기억] + [code: dentbird-console-client/src/f-shared/lib/mf/{RuntimeModule.ts, index.ts}, vite.config.ts]
- **iframe 도입에 필요했던 인프라 설정** (사용자 기억 흐림 → 코드로 발굴):
  - `staticwebappConfigs/staticwebapp.config.json` `globalHeaders`: `Cross-Origin-Embedder-Policy: require-corp`, `Cross-Origin-Opener-Policy: same-origin`, `Cross-Origin-Resource-Policy: cross-origin`, `Referrer-Policy: strict-origin-when-cross-origin`. [code: module-monorepo/staticwebappConfigs/staticwebapp.config.json]
  - webpack devServer에도 동일 COOP/CORP/COEP 헤더. [code: apps/viewer-module/webpack.config.js]
  - azure-pipelines가 빌드마다 이 config를 각 모듈 dist에 복사. [code: azure-pipelines/templates/build-pipelines.yaml]
  - **추론**: COEP+CORP는 iwtk WASM/SharedArrayBuffer 사용을 위한 cross-origin isolation, Referrer-Policy는 호스트 검증(ALLOW_ORIGIN_LIST)용. [추론 — 정확한 의도는 인터뷰 확인]
  - postMessage cross-origin 통신 + **referrer 화이트리스트 검증**: `ALLOW_ORIGIN_LIST = [CLOUD_URL, CROWN_URL, CONNECT_URL, BATCH_URL, MODELER_URL, MILLING_URL, ...]`, Electron이면 `file://` 처리. [code: apps/explorer-module/src/app/Shared/{Utils/postMessage.ts, Utils/getReferrer.ts, Constants/iframe.ts}]
  - 모듈별 **독립 도메인** 서빙: `module-explorer.dentbird.com`, `module-viewer.dentbird.com` 등 (Azure Static Web Apps). [code: apps/viewer-module/.env.prod]

### ② 이관: 빌드타임 통합 선택 (libs/embed-modules)
- 4개 앱이 `dentbird-solutions` **단일 모노레포로 합쳐지면서**, iframe을 그대로 들고 오지 않고 **빌드타임 통합(React 컴포넌트)** 으로 전환. [기억] + [code]
- **근거**: 모노레포라 빌드에 포함시키기 쉬워짐. 모듈 기능 **중복 존재 문제는 모노레포로 해결**됨. **당시 배포해야 하는 앱이 너무 많았던 것**도 빌드타임 통합을 택한 배경(관련 문서 레포 내 존재 추정 — `MODULE_MIGRATION_*.md`). [기억] + [code]
- 남은 "모듈 변경 시 전 앱 배포" 문제는, 조직 개편·레포 이관 + **모든 앱 통합 배포 전략**으로 전환되어 (당시엔) 문제가 아니게 됨. [기억]
- 구현: `libs/embed-modules/src/dialogs/`에 `SettingDialog/ExplorerDialog/ExportDialog/ViewerDialog/ShareCaseDialog`. `useEmbedModule`(= 기존 `useModuleContext` 호환 alias)로 호출부 변경 없이 전환. axios 등은 Context 주입(postMessage → props/callback). [code: libs/embed-modules/src/{index.ts, provider/, dialogs/}] [vault: decisions/module-migration.md]

### ③ 회귀: 다시 iframe 런타임 통합 (apps/cloud-desktop) + 도메인 통합
- 통합 배포 전략을 도입했지만 **시간이 지나보니 통합 배포가 실제로 된 적이 많지 않았고**, 모듈 기능이 바뀌면 어쨌든 각 앱이 모두 배포돼야 하는 건 여전히 불편. [기억]
- 게다가 **사내 배포 프로세스가 짧은 주기 배포가 어려운 방향으로 바뀌며** 배포 부담이 커짐 → 이 문제로 **다시 iframe 기반 런타임 통합으로 회귀**. [기억]
- **도메인 변경** (이 작업은 본인이 한 게 아님 — 이유/이점 학습 대상): 초기엔 모듈별 도메인(`module-explorer.dentbird.com`)이었는데, 현재는 **cloud 도메인 하위**로 빼서 `ai.dentbird.com/cloud/module.html#/<module>`로 서빙. [기억] + [code: libs/embed-modules/src/host/useModuleHost.ts `moduleRoute() → /cloud/module.html#/explorer`]
  - **same-origin 통합의 이점** [code 근거 + 추론]:
    - `module.html`이 host와 **동일 origin**(`ai.dentbird.com`) → CORS 제약 제거.
    - 같은 origin이라 **쿠키/세션/인증 자동 공유**. (iframe 모듈 전용 auth 초기화 흔적: `AuthService.initializeForIframeModule`) [code: apps/cloud-desktop/src/module-app/ModuleApp.tsx]
    - postMessage origin 검증이 `event.origin !== window.location.origin` 한 줄로 단순화. [code: libs/embed-modules/src/host/ModuleIframe.tsx]
    - `frame-ancestors 'self'` 류 CSP 단순화. [추론]
- 구현: vite/rspack **멀티 엔트리**(`index.html` + `module.html`), iframe 내부는 `HashRouter`로 `/explorer|export|setting|viewer|share` 라우팅, host는 `ModuleHostProvider`+`ModuleIframe`로 postMessage 수신. 아키텍처: `ModuleProvider`/`EmbedModuleProvider`(전체 Dialog 트리 직접 임베딩) → **경량 `ModuleHostProvider` + `useModuleHost().export.open()`**(typed same-origin postMessage). [code: apps/cloud-desktop/{vite.config.ts, src/module-main.tsx, src/module-app/ModuleApp.tsx}, libs/embed-modules/src/host/]
- **⚠️ 기여 주체 주의**: 이 ③단계(VTK/iwtk→Three.js + 최종 iframe 마이그레이션)의 결과보고서는 **타인 작성**(Confluence authorId가 본인과 다름). 본인은 "도메인 변경은 내가 한 게 아니다"라고 명시. **아래 정량은 MFE 진화의 결과이지 곧바로 본인 단독 성과 아님 — 본인 기여분(초기 module-monorepo iframe 주도, console-client MF, embed-modules 관여)과 분리해서 본문에 쓸 것.** [Confluence: page/2282094594 "VTK/iwtk → Three.js 전환 및 embed-modules iframe 마이그레이션 결과 보고", 작성자 타인]

---

## 2. 측정 가능한 변화

- **초기 module-monorepo**: 모듈 앱 5개(explorer/export/setting/viewer + module-testbed). iwtk 버전 — viewer `@imago/iwtk.js@1.9.2`, export `@2.5.784`. [code: apps/*/package.json]
- **통합 구조 전환**: 모듈별 독립 도메인(N개 SWA 엔드포인트) → cloud 단일 도메인 하위 `/cloud/module.html` (1 호스트). [code]
- **③단계 결과 정량** (⚠️ 결과보고서 타인 작성 — 기여 주체 분리 전제) [Confluence: page/2282094594]:
  - cloud-desktop 배포 번들 **119MB → 51MB (-68MB, -57%)**
  - **iwtk WASM 런타임 ~35MB + iwtk 리소스 68MB 완전 제거** → 초기 로딩 시 68MB 다운로드 사라짐
  - crown-client JS 번들 **17.61MB → 15.16MB (-13.9%)** (Dialog 트리가 iframe으로 분리됨)
  - cloud-desktop iwtk 직접 import 2개 → 0개, `download-iwtk` 빌드 스텝 제거 → CI 단순화
  - 관련 PR #6485 ~ #6503 다수
- 본인 단독 기여분(초기 iframe MFE)의 정량 지표(배포 횟수 감소 등)는 출처 없음. [측정필요]

---

## 3. 후속 영향

- **진동 자체가 후속 영향의 연쇄**: iframe→빌드타임은 "모노레포 통합"이 가능케 했고, 빌드타임→iframe은 "배포 프로세스 변화"가 되돌림. → 통합 전략은 **조직/인프라 제약의 함수**임을 보여주는 사례.
- 빌드 도구도 동반 진화: cloud-desktop **Webpack → Rspack** 마이그레이션 완료(2025-11-12, TS7056 회피 + 속도). [vault: decisions/cloud-desktop-rspack-migration.md]
- export 모듈은 **iwtk WASM 의존** 때문에 별도 트래킹(`download-iwtk`/`link-peer-iwtk` NX 타겟). [vault: decisions/export-module-migration.md]
- **iwtk → Three.js 전환** (★ 별도 findings 카드 예정): iwtk 의존성을 최대한 없애 **우리 팀이 이 모듈에 대한 제어권을 최대한 가져오려는** 동기. `cloud-mesh-io` 라이브러리 신규 구축(Three.js 네이티브 메시 I/O — DRC/STL/PLY/OBJ/VTP, DentalCameraControls, VTK 시각 동등성). **VTK.js vendor lock-in 해소**. [Confluence: page/2282094594] [기억: 사용자가 "나중에 findings로 추가" 요청]

### iframe 통합의 트레이드오프 (Q2 답 — 결과보고서 §6)
- **iframe 로딩 지연**: 모듈 열 때 iframe document 파싱 + React 마운트 수백ms 추가.
- **메모리 격리 비용**: iframe마다 별도 JS 힙 → React/MUI 등 **동일 라이브러리 중복 로딩**.
- **File 전달 제한**: postMessage structured clone으로 File/Blob 전달은 되나 일부 워크플로(importTo 등) 추가 설계 필요.
- 코드 차원 통증(본인 관여 흔적): iframe이 별도 browsing context라 **폰트 CSS 진입점 재로드**, **auth iframe 전용 초기화**(`initializeForIframeModule`). [code: cloud-desktop/src/{module-main.tsx, module-app/ModuleApp.tsx}]

---

## 4. 운영 패턴

- 초기 module-monorepo에서 4개 모듈을 **약 5개월 지속 관리**: 사용자 커밋 **55개**, 2024-11-21 ~ 2025-04-04. 2024-11 중순 모듈 4개 이동/추가 집중, 이후 iwtk 버전 고정·i18n·SSE·버전 릴리스(explorer v1.0.6, export v1.1.1). [git: module-monorepo, --author jwkim]
- 이후 embed-modules 이관·iframe 회귀 작업에도 관여(코드 곳곳에 흔적). [code]

---

## 미해결 (출처 확인 필요)

- **★ 기여 주체 분리 (본문 작성 시 최우선)**: ①초기 iframe MFE(module-monorepo)는 본인 주도(55커밋), console-client MF 설정 본인, embed-modules 이관 관여 = **확실한 본인 기여**. 반면 ③단계 VTK→Three.js + 최종 iframe 결과보고서(번들 -57% 등)는 **타인 작성/주도** → 본인 카드엔 본인 기여분만, ③ 정량은 "팀 결과/맥락"으로만 인용. [Confluence authorId 대조]
- **iframe 인프라 헤더의 정확한 도입 이유**: COEP/CORP가 iwtk WASM(SharedArrayBuffer) 때문이었는지 — 본인 확인 필요. [기억 확인]
- **iframe ↔ 빌드타임 회귀 시점**: vault decision(2026-05-20)은 "iframe→React 컴포넌트(빌드타임)" 진행 중으로 기록. 이후 다시 iframe 회귀가 현재 코드 상태(결과보고서 2026-04-15) — 타임라인 정합성 한 번 확인. [기억 확인]
- **통합 배포 전략이 실제로 거의 안 됐다는 점**: 정성 근거(왜 안 됐는지) 한 겹 더. [기억]
- **iwtk → Three.js / cloud-mesh-io는 별도 findings 카드로** 분리 발굴 예정 (사용자 요청). 본인 기여 범위 확인 포함.

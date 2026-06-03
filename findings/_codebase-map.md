# _ 코드베이스 맵 — dentbird-solutions 모노레포 구조

> 면접 "코드 구조를 설명해보세요"용. NX + pnpm 모노레포. apps 30+개 / libs 60+개. (코드 확인 2026-06-03, read-only)
> ⭐ = 본인 작업과 직접 연관(이력서 카드 근거). 나머지는 서비스 이해용.

---

## APPS

### 클라이언트 (FE)
- **account-client** ⭐ — 계정·구독 관리 FE(React/Vite). → 04 Account
- **cloud-desktop** ⭐ — Cloud 메인 데스크톱 FE. 모듈 iframe host + 런타임 config + module.html. → 03 MFE, 01
- **cloud-mobile** — Cloud 모바일 FE.
- **payment-client** — 결제 관리 UI.
- **crown-client** (+ crown-client-official) — Crown CAD 임상 SW(Rspack). iwtk/CAD 코어.
- **crown-bo** — Crown 백오피스.
- **milling-client** — Milling CAM 클라이언트(Rspack).
- **modeler-client** — 3D Modeler 앱(Rspack).
- **dealer-backoffice-client** — 딜러 백오피스.
- **batch/batch-web** — Batch 관리 웹 UI(React/Vite).
- **linker-desktop** — Linker Electron 앱(DIN·CAD 링킹). → 02 Linker
- **launcher-desktop / launcher-main-client** — Electron 런처(메인 프로세스/렌더러).
- **traffic-dashboard** — 트래픽 모니터링 대시보드.

### 서버 (Kotlin/Spring — `apps/platform/projects/server/`)
- **platform-server** ⭐연관 — 핵심 플랫폼: 인증 세션·AEAD 파일 암호화·파일 스토리지. → 10
- **api-gateway-server** — API 게이트웨이: 인증 필터·리전 라우팅. → 10
- **account-server** — 계정·구독·SaaS. payment-server — 결제·청구.
- **dentbird-console-server** — 관리자 콘솔 BE. migration-app — DB 마이그레이션.

### 서버 (Node) / 워커
- **milling-server** — Milling CAM BE(Node/TS).
- **milling-worker-server** — Crown 생성 오프로드 워커. → 백엔드 통합
- **cloud-storage-proxy-lambda** ⭐연관 — S3 스토리지 프록시(AEAD decrypt 경로). → 10
- **batch/batch-worker-server**, **batch-native**, **batch-cli** — Batch 처리·CLI.
- **legacy-batch/** — 레거시 Electron 배치(batch-main-client 등). → 05 Batch

### 공통 모듈 (구 iframe, `apps/*-module`)
- **explorer-module / export-module / viewer-module / setting-module** — 구 module-monorepo 잔재(현재 embed-modules로 통합). → 03 MFE

### 테스트 / 도구
- **e2e** ⭐ — 통합 E2E(Playwright, 642스펙·13제품). → 09
- **crown-e2e** — Crown 전용 E2E. **testRunner/** — core/crown-flow/qms/server 테스터.
- **dev-proxy / dev-proxy-tray** — 로컬 개발 프록시.

---

## LIBS (카테고리별)

### 설정·런타임 / 인프라
- **runtime-config** ⭐ — `getConfig`/`deriveUrls`/`detectProduct`. 런타임 config 핵심. → 01
- **cloud-i18n**, **batch-environment** — 다국어·환경 설정.

### API·통신
- **platform-api-client** ⭐연관 — 플랫폼 API + 파일 접근 정책·복호화(WebCrypto decrypt). → 10
- **api-client / fetch-client / axios-utility / express-utility** — HTTP 클라이언트·재시도·에러.

### UI·디자인
- **imago-cloud-design-system** ⭐ — MUI 기반 디자인 시스템(모노레포 내). → 07
- **imago-theme / ui-components** — 테마·타이포·UI 유틸.
- **embed-modules** ⭐ — 공통 모듈 iframe host + Dialogs(Viewer/Export/Explorer/Setting/Share). → 03 MFE
- **error-boundary** — React 에러 바운더리.

### 3D·렌더링
- **cloud-mesh-io** ⭐ — Three.js 메시 I/O(DRC/STL/PLY/OBJ/VTP, DentalCameraControls). → 08
- **mesh-rendering-object**(iwtk 기반, 레거시), **object3d-v6**, **render-primitives**, **geometric-utility**, **compute-contact-distance**(WebGPU).

### 인증·세션
- **auth-core-sdk / auth-react-sdk / auth**(CaseLockManager) — 인증 SDK. → 10
- **session-heartbeat**(SessionGuard), **heartbeat-scheduler**(activityDetector), **wip-lock**(useWipLock) — 세션 폴링·동시편집 잠금. → 10, 백엔드 WIP Lock

### 도메인·비즈니스
- **crown-case / crown-core-module / crown-io / crown-shared-setup / crown-client-spec-reconciler** — Crown 도메인.
- **cloud-core / cloud-components / cloud-hooks / cloud-states(Recoil) / cloud-credit-pdf** — Cloud 도메인.
- **milling-front-module / milling-cam-spec** — Milling 도메인.
- **front-module**(크레딧·구독·iframe), **imago-modules**(구 모듈 시스템, 레거시).

### 측정·CRM
- **ga4** ⭐연관 — GA4 추적(`track`/`identifyGA4User`). → 측정
- **customer-io** ⭐연관 — Customer.io CRM 메시징 + Feature Flag. → 마케팅

### 테스트
- **e2e/shared** ⭐ — Page Object·fixture·세션. **e2e/tc-manager-reporter** ⭐ — TC Manager Playwright 리포터. → 09
- **spec-reconciler**, **i18n-review / i18n-test-utils**.

### 유틸·기타
- **client-URIPathGenerator / url-utils / object-utility / animation-utility / mouse-event / libarchive-shim / output / package-extends / server-permission / solution-shared-types / devtools / web-design-processor**.

---

## 본인 연관 정리 (이력서 카드 ↔ 코드)
| 카드 | 핵심 코드 |
|------|-----------|
| 01 인프라 | `libs/runtime-config`, `docker/stack/setup.sh`, `tools/dev-stack` |
| 03 MFE | `libs/embed-modules`, `apps/cloud-desktop/module.html`, `apps/*-module` |
| 04 Account | `apps/account-client` |
| 08 iwtk→Three.js | `libs/cloud-mesh-io`, embed-modules ViewerDialog |
| 09 테스트 | `apps/e2e`, `libs/e2e/*`, `.claude/skills/e2e` |
| 10 인증/보안 | `platform-server`(AEAD·세션), `api-gateway-server`, `libs/platform-api-client`, `auth-*`, `cloud-storage-proxy-lambda` |
| 07 디자인시스템 | `libs/imago-cloud-design-system` (+ 별도 `imago-design-system` 레포) |
| 배포 운영 | `.github/workflows/deploy-{clients,servers}.yml`, `.claude/rules/deployment-compat.md` |

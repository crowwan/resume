# Dentbird Batch — 크라운 디자인 배치 처리 Electron 앱 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 조회일 표시 생략
> 레포: `workspace/dentbird-batch-client` (Bitbucket: `imagoworks/dentbird-batch-client`) — **초기 Batch v1 세대**
> 기존 `findings/05-batch.md`(빌드 파이프라인·자동업데이트·배포 중심)와 **중복 없이 병합 가능한 NEW 사실**만 적재.
> ⚠️ vault의 VTK→three·pause/abort·module-migration 은 **P004(후속 세대) 맥락** — 본 카드(batch-client v1)와 혼합 서사 금지.

---

## 본인 기여 식별 요약

- **batch-client v1 = 본인 최다 기여자** — 347커밋 / 전체 1위(2위 yjka 126, 3위 ajin 110), 커밋 기간 2023-12-29 ~ 2025-01-24, 태그 25개 `[git: shortlog -sne]` `[git: tag]`
  - ※ 347커밋·1위 사실은 기존 `05-batch.md §0`에 이미 존재(중복) — 본 파일에선 **그 위에 얹는 IPC/딥링크/인터셉터/VTK/WASM 영역**이 NEW.
- **본인(jwkim) 단독·주도로 식별되는 작업**: crown window 프로세스 분리(DB-248/PR #117), 타입드 IPC 채널 체계, contextBridge 도메인 네임스페이스, HTTPS 프로토콜 인터셉터+Origin 위조(DB-142/DB-104), 딥링크 커스텀 프로토콜+환경별 스킴(DB-63/PR #136), single-flight 토큰 갱신 인터셉터(DB-565/PR #226), 세션 만료 처리, API Gateway 전환+JWT 디코드(DB-594/DB-155), VTK 3D 뷰어 마우스 인터랙션 통일(DB-580/PR #236, Approved-by Yunji Ka = 본인 단독 구현), crashReporter 도입(DB-521/PR #222), power-save blocker(DB-469/PR #195), refresh token 도메인 격리(DB-422/PR #165), DB-324 강제종료 버그 해결(PR #139). `[git/jira: 각 PR·티켓 본인 author/assignee]`
- **본인 기여 아님 (서사화 금지)**:
  - **[팀-Hyejin]** DB-522 리스트 패치 최적화(useQuery→useInfiniteQuery), author hjnamkung, jwkim은 Approver `[git: c7662ba PR #218]`
  - **[팀-David]** `PerformanceMonitor` 클래스(performance.mark/measure 싱글톤, electron-log 실행시간 로깅), DB-564 author David.ryu / PR #224 — jwkim은 **사용·배치자**(WASMMemoryMonitor를 CrownModuleProcessor 자식으로 이동) `[git: 2d9ce33~810bae4]`
- **세대 경계**: vault decision(2026-06-05 VTK 썸네일 three 통합 보류 ADR)은 **P004 세대** 작업이므로 본 카드와 구분.

---

## 1. 의사결정 흔적

### Crown 모듈을 별도 BrowserWindow로 프로세스 분리 (DB-248, PR #117) — 핵심 아키텍처 결정 ★NEW
- 크라운(WASM 크라운 생성) 처리를 메인 UI 윈도우에서 분리해 **전용 hidden crown window**로 격리 `[git: b06df07 / PR #117]` `[jira: DB-248 "Crown module 기능 window 분리"]`
- crown window 구조: 별도 `BrowserWindow`, **공용 preload 1개 공유**, hash 라우팅 `#/crown`으로 같은 React 앱을 다른 진입점 로드, `parent: mainWindow`, **`backgroundThrottling: false`**(백그라운드에서도 처리 속도 유지), `contextIsolation: true`, `nodeIntegration: false`, 프로덕션 `show: false` `[code: src/main/window/crownWindow.js]`
- 이 PR에서 **타입드 IPC 채널 체계 도입**: `RENDERER_TO_CROWN_CHANNEL`(crown:start/close/crash) + `CROWN_TO_RENDERER_CHANNEL`(renderer:get-header/invalidate/done/error/retry-failed/closed) 신설 `[code: src/common/constants/channels.js]`
- crownModuleManager 재시도 상수(`DEFAULT_RETRY_COUNT=0`, `MAXIMUM_RETRY_COUNT=3`), 네트워크 에러 시에만 `stopCrownModuleProcessing` 동작하도록 조건 분기 `[code: crownModuleManager.js]`

### IPC contextBridge 도메인별 네임스페이스 설계 ★NEW
- preload `contextBridge.exposeInMainWorld`로 **도메인별 API 분리 노출**: `electronAPI`·`caseAPI`·`projectAPI`·`exportPathAPI`·`titleBarAPI`·`crownModuleCommunicationAPI` `[code: src/preload/preload.js]`
- main 측 `mainModule/`(setupDefaultProtocol·SingleInstance·HandleRefreshToken·HttpsProtocol·RequestOrigin·FileProtocol·AutoUpdater·AppCrashReporter) + `features/`(Auth·CaseImport·CrownModule·Export·FileRead·TitleBar handler/manager 페어) **setup 함수 단위 모듈화** `[code: src/main/mainModule/index.js, src/main/features/]`

### HTTPS 프로토콜 인터셉터 + Origin 위조 (DB-142, DB-104 선행) ★NEW
- 초기: `session.defaultSession.webRequest.onBeforeSendHeaders`로 모든 요청에 `Origin: https://batch.dev-dentbird.com` 강제 주입(main.js 인라인) `[git: c7eb8da]`
- DB-142에서 **`setupRequestOrigin` + `setupHttpsProtocol(mainWindow)`로 함수 분리·모듈화**, `setupFileProtocol`(DB-104) 대체 `[git: c7eb8da, 6661051, f16fad7]` `[jira: DB-104 "file protocol handler 추가"]`
- 배경: 데스크탑 앱(`file://` 출처)이 `batch.dev-dentbird.com` 웹앱처럼 서버 인증받기 위해 **Origin을 웹 도메인으로 위장**. `localhost`→`127.0.0.1` 변경 시 `BATCH_ORIGIN_URL` 도입 `[git: b057868]`

### 딥링크(웹→앱) 커스텀 프로토콜 + 환경별 분리 (DB-63, PR #136) ★NEW
- `batch://login/success` 커스텀 프로토콜로 **웹 로그인 → 앱 기동/포커스** `[git: 31d6935]` `[jira: DB-63 "Batch custom protocol 적용"]`
- 플랫폼 분기: **win32**은 `app.on('second-instance')`에서 `commandLine.pop()`으로 콜백 URL 수신, **macOS**는 `app.on('open-url')` `[code: setupSingleInstance.js]`
- 단일 인스턴스 락(`requestSingleInstanceLock`)으로 중복 실행 차단 + 기존 윈도우 restore·focus, win32에서 콜백 URL이 프로토콜로 시작하면 `loginSuccessHandler(mainWindow, callbackUrl)` 호출 `[code: setupSingleInstance.js]`
- **환경별 스킴 분리**(PR #136): dev=`dentbird.batch.dev`, prod=`dentbird.batch`를 `.env`로 주입 → QA/PROD 앱 같은 PC 공존 시 충돌 방지 `[git: 76ac6e1]`
- 연관: DB-129 "windows 환경 authenticate 기능 구현"(딥링크 win32 분기), DB-90 "Windows 타이틀 바 기능 구현"(titleBarAPI getPlatform — VTK pan 플랫폼 분기 토대) `[jira: DB-129, DB-90]`

### HTTP 인터셉터 토큰 갱신 — single-flight(중복 갱신 방지) 패턴 (DB-565, PR #226) ★NEW
- crown window 전용 `CrownCoreModuleAxiosInterceptor` 신설 `[git: b243257]`:
  - **request**: 헤더 access token이 `isTokenExpired`면 일회성 disposable axios로 refresh 후 헤더 재설정(`Bearer`+`x-user-id`)
  - **response**: `401`이면 토큰 갱신 후 `originalConfig` 재요청(retry)
  - **`tokenRefreshPromiseRef`로 갱신 Promise 단일화** — 동시 다발 요청이 refresh 중복 호출 방지(성공/실패 시 ref null 리셋) `[code: CrownModuleAxiosInterceptor.tsx]`
  - useEffect cleanup에서 `interceptors.request/response.eject` + ref null로 누수 방지
- 같은 패턴이 메인 윈도우 `AxiosInterceptor.tsx`에도 동일 적용 `[git: 1ea5820]`
- **트리거 = DB-565**("crown 작업 완료 케이스가 batch list에 안 보임" Dev Bug) — 만료 토큰으로 list 패치 실패가 근본 원인 계열 `[jira: DB-565]`

### 세션 만료 처리 — crown 작업 중 만료 시 윈도우 닫기 (DB-565) ★NEW
- IPC 채널 `RENDERER_SESSION_EXPIRED('renderer:session-expired')` 신설, preload `crownModuleCommunicationAPI.onSessionExpired` 추가 `[git: 94e27db]`
- crown 작업 중 세션 만료 → **crown window 닫고 main window 알림** `[git: 94e27db, 25d00f6, 32d8e29]`
- removeAllListeners에 session-expired 누락 버그 수정(리스너 누수) `[git: 7de2649]`

### API Gateway 전환 + JWT 디코드 (DB-594, PR #238 / DB-155 선행) ★NEW
- accessToken을 **API Gateway(`api.dev-dentbird.com/account`) 경유 발급**으로 전환, `REACT_APP_ACCOUNT_SERVER_BASE_URL` 도입 `[git: 150c0d2]`
- `jwt-decode`로 access token에서 userId·email 추출(`decodeToken`), 로그아웃 후 재로그인 시 헤더에서 access token 제거 `[git: 81c1c90, 33a6ef0, cc21f16]` `[jira: DB-155 "변경된 jwt token 구조에 맞게 token 설정"]`

### main 프로세스 번들링(webpack) 도입 ★NEW(상세)
- main 코드 webpack 번들링, 번들 output gitignore, **빌드 후 preload·loadURL 경로 분기**(`app.isPackaged`별 `output/preload` vs `preload`) `[git: 5c08822, f2ac55f, 352dc06]`
  - ※ 기존 `05-batch.md §5`에 "webpack 빌드 스크립트" 언급은 있으나 **main 번들링·경로 분기 디테일은 NEW**.

---

## 2. 측정 가능한 변화 (출처에 적힌 값만)

- **remote process 15분 타임아웃**: 서버 처리(`processOnRemoteServer`)가 **15분(`1000*60*15`ms)** 지속 시 `Promise.race`로 `CASE_GENERATION_TIME_OUT` reject → 서버에 `CROWN_GENERATION_FAILURE`로 failed 처리. 무한 대기로 배치 큐 멈춤 방지 `[git: 7ff55d9, 코드값]`
- **재시도 한도 `MAXIMUM_RETRY_COUNT = 3`** (기본 `DEFAULT_RETRY_COUNT = 0`) `[code: crownModuleManager.js]`
- **WASM 메모리 모니터링**: `getCurrentWasmMemorySize()`를 `bytesToMegaBytes` 변환해 **MB 단위·1초 주기** 그래프(graphWidth 274). dev(`NODE_ENV==='development'`)에서만 활성 `[code: WASMMemoryMonitor.tsx]`
- (참고) 빌드 시간 20분+→6분, 버전 13개 등은 기존 `05-batch.md`에 이미 있음 — 여기 비중복.

## 3. 후속 영향

- DB-248의 hidden crown window + 타입드 채널 구조가 이후 **세션만료(DB-565)·power-save(DB-469)·crash reporter(DB-521)·toast 에러(DB-573)·400 에러 처리(DB-528)** 등 모든 crown 관련 기능의 토대 — 같은 IPC 채널/preload API에 계속 확장 `[git: 채널 상수 누적]` `[jira: DB 티켓군]`
- DB-324 강제 종료 버그 이후 **crown window 생성 시 항상 `parent: mainWindow`** → 이후 power-save blocker도 같은 manager에서 window close에 묶어 정리 `[git: 14be03f → 64228af]`
- single-flight 토큰 갱신 패턴이 crown·main window 양쪽 인터셉터에 동일 적용 `[git: b243257, 1ea5820]`
- **VTK reviewer 뷰어의 후속 운명(세대 교체)**: 본 batch-client에서 본인이 만든 iwtk/VTK 기반 reviewer 3D 뷰어(DB-580)는 이후 **P004 세대에서 viewer가 IWTK→three.js로 마이그레이션**됨. 단 **썸네일 생성 엔진은 VTK 잔존** — jwkim이 2026-06-05 "VTK 썸네일을 three로 통합하지 않고 책임 경계로 공존" ADR 결정 `[vault: decisions/2026-06-05-vtk-thumbnail-three-migration-deferred.md, author jwkim]` — ※ **다른 세대(P004) 작업**, 세대 구분 필요.

## 4. 운영 패턴 (반복 담당 + 회귀/버그 대응)

- **DB-324 강제 종료 후 앱 실행 불가 버그 해결**(PR #139): main window 강제 종료 후에도 crown window가 죽은 main 인스턴스를 들고 있던 문제 → `createCrownWindow(mainWindow)` parent 전달, win32 `mainWindow.on('close', () => app.quit())`로 완전 종료 보장 `[git: 14be03f, DB-346 포함]`
- **DB-373 processing 중 crown window 종료 시 dialog** — 처리 중 crown window 종료 사용자 가드 `[jira: DB-373]` `[git: crown window 라이프사이클 계열]`
- **DB-469 처리 중 절전 모드 차단**(PR #195): crown 동작 중 화면 꺼져도 안 멈추게 `powerSaveBlocker`, crown window close 시 `powerSaveBlocker.stop` 정리 `[git: 64228af]`
- **DB-521 Electron crashReporter 도입**(PR #222): main에 `crashReporter.start({ compress: false, uploadToServer: false })` — `setupAppCrashReporter` 모듈 분리 `[git: 6e1589c, 586185b]` `[jira: DB-521]`
- **crown renderer 에러 시 윈도우 안 멈추게 변경**(DB-573/DB-568 계열): 직접 send된 에러에서 `stopCrownModuleProcessing`/`closeCrownModule` 호출 제거 → 단일 에러로 전체 배치 중단 완화 `[git: 0c2461e]` `[jira: DB-573, DB-568]`
- **VTK.js 3D 뷰어 Win/Mac 마우스 인터랙션 통일**(DB-580, PR #236): 사내 `iwtk.js`(VTK 확장) reviewer 뷰어의 pan/rotate/zoom manipulator 직접 교체 `[code: .../ReviewerDialogue/Utils/makeInteractorStyleAsDefault.ts]`
  - 기본 manipulator(pan/zoom/roll) 제거 후 커스텀 rotate(`iwtkMouseCameraTrackballRotateManipulator`)·zoom·pan 재구성, **roll 제거** `[git: 377c2a6]`
  - **플랫폼별 pan 분기**: `window.titleBarAPI.getPlatform()`(IPC로 OS 조회, DB-90 titleBar 토대)로 분기 — **win32 Ctrl+좌클릭 드래그 pan**, **macOS keyboard-event 기반 커스텀 pan(`CustomMouseCameraTrackballPanManipulator`)+Ctrl+좌클릭 pan**까지 추가해 양 OS 조작감 통일 `[git: 14e7cac, 395b524]`
  - manipulator `.d.ts`·`.mjs` 직접 작성, 경로 `Utils/` 정리 `[git: 54f7d5e, 71302c4, b2ce1b5, 6db79bd]`
  - PR #236 Approved-by: Yunji Ka (**본인 단독 구현**) `[git]`
- **WASM 메모리 모니터 위치 재배치**: `WASMMemoryMonitor`를 `CrownModuleProcessor` 자식으로 이동(처리 컨텍스트 안 메모리 추적), `CrownModuleProcessor`가 `PerformanceMonitor.getInstance()` 사용 `[git: 0b05754]`
  - ※ `PerformanceMonitor` 클래스 자체는 **[팀-David]**(DB-564, PR #224, author David.ryu) — jwkim은 사용·배치 측 `[git: 2d9ce33~810bae4]`
- **refresh token 도메인 격리**(DB-422, PR #165): `onBeforeSendHeaders`에서 URL에 `dentbird.com` 없으면 Cookie의 refresh token(`batchRefreshToken`/`cloudRefreshTokenV2`/`refresh-token-user`) 제거 → 외부 도메인 인증 쿠키 유출 방지 `[git: 57f7f9f, 33a6ef0]`
- **자동 로그인 미설정 시 종료 시 refresh token 쿠키 삭제**(DB-594): `checkAutoLoginSetting`에서 비자동로그인이면 3종 refresh 쿠키 제거 `[git: 150c0d2]`
- **네트워크 재연결/오프라인 처리**: `OnlineConnectionChecker`가 오프라인이면 crown module 닫고 `NetworkErrorPage` 표시(DB-187 재연결 restore dialog) `[code: OnlineConnectionChecker.tsx, git: 7ec75a0 PR #80]`
- **storage 부족 IPC 채널**(`RENDERER_NOT_ENOUGH_STORAGE`): crown 처리 전 저장 용량 부족 시 별도 채널 알림 `[code: channels.js]`
- **crown 파라미터/세팅 반복 담당**: DB-593(crown parameter api 변경), DB-326/DB-171/DB-137/DB-133/DB-125/DB-124/DB-123(crown setting 필드명·범위·default·변경사항·convert) — crown 설정 IPC 경계 반복 담당 `[jira: DB-593, DB-326, DB-171, DB-137, DB-133, DB-125, DB-124, DB-123]`

## ★ 기존 findings/이력서에 없던 새 증거

기존 `05-batch.md`는 **빌드 파이프라인·자동업데이트·배포·코드사인** 축만 다룸. 아래는 그와 **다른 축(런타임 아키텍처)** 으로 전부 NEW:

1. **Crown 모듈 프로세스 분리(전용 hidden BrowserWindow) + 타입드 IPC 채널 체계** (DB-248/PR #117) — 멀티프로세스 Electron 아키텍처 설계 증거. `backgroundThrottling:false`로 백그라운드 처리 속도 보장. `[git: b06df07]`
2. **single-flight 토큰 갱신 인터셉터** (DB-565/PR #226) — 동시 다발 요청의 refresh 중복 호출을 `tokenRefreshPromiseRef` 단일 Promise로 방지. crown·main 양쪽 동일 적용. `[git: b243257, 1ea5820]`
3. **딥링크 커스텀 프로토콜(웹→앱) + 환경별 스킴 분리 + 단일 인스턴스 락** (DB-63/PR #136) — win32(second-instance) / macOS(open-url) 플랫폼 분기. dev/prod 스킴 분리로 QA·PROD 공존 충돌 방지. `[git: 31d6935, 76ac6e1]`
4. **HTTPS 프로토콜 인터셉터 + Origin 위조** (DB-142) — `file://` 데스크탑 앱이 웹 도메인 인증을 받도록 Origin 위장. 보안/네트워크 경계 설계. `[git: c7eb8da]`
5. **refresh token 도메인 격리** (DB-422/PR #165) — 외부 도메인 요청 시 인증 쿠키 제거(쿠키 유출 방지). 보안 판단 증거. `[git: 57f7f9f]`
6. **15분 remote 타임아웃 + 재시도 3회** (`Promise.race`) — 무한 대기로 배치 큐 멈춤 방지. 배치 안정성 설계. `[git: 7ff55d9]`
7. **VTK.js 3D 뷰어 Win/Mac 마우스 인터랙션 통일** (DB-580/PR #236, 본인 단독) — 사내 iwtk.js manipulator 직접 교체·작성, OS별 pan 분기로 조작감 통일. `[git: 377c2a6, 14e7cac]`
8. **power-save blocker / crashReporter / 오프라인 처리** 등 데스크탑 앱 운영 안정화 패턴 (DB-469/DB-521/DB-187). `[git: 64228af, 6e1589c, 7ec75a0]`
9. **Jira 21건 본인 assignee 교차검증** (project=DB, status=종료) — crown/IPC/딥링크/토큰 영역 커밋을 티켓 단위로 뒷받침. `[jira: DB]`

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

| 항목 | 상태 |
|------|------|
| 15분 타임아웃·재시도 3회 실제 운영 효과(타임아웃 발생 빈도 등) | `[측정필요/인터뷰]` |
| WASM 메모리 모니터가 실제 OOM/누수 잡은 사례 | `[측정필요/인터뷰]` — 코드상 dev 전용 모니터까지만 확인 |
| crown window 프로세스 분리의 정량 효과(메인 UI 프리징 개선 등) | `[측정필요/인터뷰]` — 분리 동기는 코드로 확인, 효과 수치 없음 |
| 딥링크 로그인 토큰 핸드오프 상세(`loginSuccessHandler` 내부 토큰 처리) | `[코드 추가확인 가능]` |
| DB-373 dialog가 본인 단독인지(연관 sub-task 다수) | `[기여확인]` — assignee는 jwkim, 구현 범위 인터뷰 보강 |
| Jira description 본문(대부분 null) — 티켓별 상세 배경 | `[인터뷰 보강]` — 요약·범위만 확보 |
| VTK reviewer 뷰어(DB-580)가 batch-client v1 산출물 vs 이후 세대 | `[기억]` — 본 카드는 v1 슬라이스로 한정, P004 viewer 마이그레이션과 구분 필요 |

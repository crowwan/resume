# 포트폴리오 케이스 백로그 — 세션 전수 발굴 (2026-06-08)

> 5개 발굴 에이전트가 vault(decisions·learnings·bugs) + Claude 세션 180+ (`dentbird-solutions{,2,3}`)를 전수 인덱싱한 결과.
> **현재 portfolio.md 11케이스에 아직 안 넣은 후보**를 보관. 나중에 검토해 선별 추가.
> 귀속은 git author 교차검증 기준. 사내 심볼은 익명화 필요. 발명 금지 — 출처 있는 것만.

---

## A. 신규 케이스급 후보 (본인 단독 진단·귀속 명확)

### A1. 리팩토링 "할지 말지" 비용/편익 사다리 ★시니어 판단
- **WHY**: 같은 강퇴/suspend 다이얼로그가 4곳 중복 → UI·i18n drift(DEN-4578·CRWN-3197/3319 회귀 3건). 통합하고 싶지만 "컨벤션에 없는 첫 사례 만드는 first-mover 비용"이 걸림.
- **HOW**: "도메인별 코드 모으기는 수단이지 목표가 아니다, 목표는 drift 차단"으로 재정의. 개입 강도 0~4 사다리(ESLint rule → i18n 키 단일화 → hook 통합 → 공유 lib)에서 **drift 비용 80%를 잡는 최저 칸** 선택. i18next `addResourceBundle`로 호출부 변경 0.
- **출처**: 세션 f9e8071a, de39bf22 / **9월 caseInfoDialogue 공통화**(history 09-30)도 같은 결.
- **귀속**: ✅ 본인 판단·설계.

### A2. 벤더 OAuth 콜백 복원력 — 1회용 code × remount race
- **WHY**: 외부 스캐너 계정(Shining3D·MeditLink) 로그인 콜백에서 "로그인은 되는데 alert만 뜨는" 모순.
- **HOW**: OAuth code는 1회용인데 `WorkspaceWorkersProvider`가 응답 도착 시 children unmount-remount → 콜백 `useEffect` 재실행 → 소비된 code로 2차 교환 → 400. `useRef` 가드도 remount에서 리셋돼 무효. **콜백을 부트스트랩 트리 밖 경량 shell로 분리**. "API 응답 속도와 연관"이라는 본인 관찰이 race 단서.
- **회고**: Shining3D만 먼저 고치고 medit/itero는 "버그 없어 유지" → 동일 잠복 버그가 medit에서 표면화. "한쪽만 고치면 안 된다"를 git 이력으로 검증.
- **출처**: 세션 8f0f25a4, d35f2fe6, 1dfe9303, 1e4d0fd9 / git 637cc6fe, 60c0efb2, 6ab175ab.
- **귀속**: ✅ 본인.

### A3. cross-app schema 계약 drift 회귀 (Preference 색 v6 cutover)
- **WHY**: "분명히 저번에 수정했는데" 회귀 반복(DEN-4385→4549→4597). texture·vertex-color 없는 스캔이 사용자 Preference 색이 아닌 고정 크림색.
- **HOW**: 세 가설을 사용자의 "흰색 아닌 고정값" 한 마디로 반증·교체하며, 진짜 원인이 **Crown(writer)이 v6 Preference API로 cutover하며 Cloud Viewer(reader)가 같은 키를 안 따라간 writer/reader schema 계약 drift**임을 코드로 확정.
- **출처**: 세션 b7739b0f, a9d2b062, 69fe3e43.
- **귀속**: ✅ 회귀 추적·진단·구조 규명 본인. ⚠️ v6 cutover(CRWN-3355)는 hckim.

### A4. promote-fast.yml stale 배포 — module.html 누락
- **WHY**: 직접 고친 feature flag provider 버그가 QA에서 재발. "단일 vite 빌드면 하위 모듈도 같이 배포됐어야".
- **HOW**: ArgoCD가 아니라 **S3 객체 last-modified 직접 비교**(index.html 5/13 vs module.html 4/30 잔존)로 코드 회귀가 아닌 배포 누락으로 전환. 원인 = `promote-fast.yml`이 index.html만 `cp`하고 `--exclude "*.html"`로 module.html 누락. `--sync --include "*.html"`로 통일.
- **출처**: 세션 c0479565, 5cf0accb / PR #9240.
- **귀속**: ✅ 본인. (Case 2와 다른 축 — 클라이언트 promote 파이프라인.)

### A5. cross-surface 일관성 — 어버트먼트가 한 화면에만 (prefetch/fresh 비대칭)
- **WHY**: DEN-3706 머지 후 cloud viewer만 어버트먼트 보이고 batch/review/share 안 보임.
- **HOW**: abutment 합류가 `prefetchViewerMesh` 한 경로에만 들어가고 fresh fallback엔 누락. SoT 통합 fix 후 격리환경 검증 시도하다 fixture 한계(암호화 payload 미시드)에 막혀 그 한계도 진단.
- **출처**: 세션 049884d2, 4caa54cd, a07e7b2f.
- **귀속**: ✅ cross-surface 일관성·회귀 본인. ⚠️ abutment 3D 렌더 선행은 별도.

### A6. 3앱 cross-app mesh 시각 회귀 CI (DEN-4549 Phase C)
- **WHY**: cloud Viewer·crown·batch ReviewPage 3앱이 같은 mesh를 공유 렌더하는데, 한 앱만 회귀시켜도 자동 감지 안 됨.
- **HOW**: 3앱 pair-wise mean‖Δ‖ baseline 박제. 신규 e2e 앱 대신 기존 multi-app spec home 하위로 위치(scaffold 제거). 3앱이 같은 fixture 인프라 공유함을 직접 추적해 확인.
- **출처**: 세션 d74c8f2f / jwkim 27커밋.
- **귀속**: ✅ 본인. (Case 6의 단일앱 시각회귀를 앱 간 정합으로 확장.)

### A7. 두 통합 방식 공존의 추상화 hole (DEN-4582 Export redirect)
- **WHY**: Cloud Case list에서 Export to CAD 시 스낵바만 뜨고 Import dialog 안 뜸. 이전 수정(#9520)이 안 통함.
- **HOW**: embed-modules가 in-process(useEmbedModule, Cloud/Batch)와 iframe(useModuleHost, Crown/Modeler/Milling postMessage) 두 추상화로 분리. 이전 PR이 경계 hole(Cloud Case list만 onSuccess 안 넘김)을 비껴감을 특정. `MODULE-ARCHITECTURE.md` 8섹션으로 박제.
- **출처**: 세션 c83b174c / PR #9676.
- **귀속**: ✅ 본인. (Case 7의 전략사 → 운영 누수 후속편.)

---

## B. 기존 케이스 보강 후보

- **Case 2/4 (빌드·격리 운영)**: EMLINK 하드링크 고갈(restore 스크립트 비대칭이 8앱 전멸) / 격리환경 최신화 중 AWS 토큰 만료·컨테이너 recreate (세션 3f31d0f3, 0cb241b4). "self-hosted 러너 상태 누적 장애" 패밀리.
- **Case 5 (AI 변경감지)**: AI 리뷰봇이 CI 실패 원인을 오진한 걸 git log·merge-base로 반증 (세션 76476aae, PR #9404). 현재 "AI가 틀린 테스트 통과" 외 "AI 진단 자체가 틀림" 두 번째 사례.
- **Case 6 (3D)**: ① 내가 만든 BFS workaround(`synthesizeVertexColorFromTexture`)를 근본원인(GENERIC UV semantic) 발견 후 `extractGenericUvAttribute`로 스스로 폐기 (세션 56117f68·07ce86ed, jwkim 42커밋) ② ContactCheckingModel "나머지는 다 scan" 폴백 z-fighting → `resolveViewerLayer` 순수함수 추출 (세션 69fe3e43) ③ 색 우선순위 3앱 횡단 명세 검증 CRWN-3294 (세션 e875aeac).
- **Case 7 (MFE)**: iframe document에 폰트 cascade 안 됨(host @font-face 미적용) / iframe ModuleApp 트리 Provider 결손(OpenFeature·Snackbar) (세션 3d41f241, 8e4ba30a, c0479565).
- **Case 9 (관측)**: Datadog 전략문서 정량 주장을 prod 직접 쿼리로 거짓 판별(facet 미등록은 거짓, @version `undefined-undefined-prod` literal 실제 결함) (세션 48bf022a).
- **Case 11 (딥링크)**: 코드베이스에 이미 있던 로컬 HTTP 헬스체크(localhost:55000/api/health) 발굴 → 휴리스틱→결정론 2단계 설계 / "3초 수렴" 1차 근거 git ffa59f46 CP-1327 (세션 f3f73a9c, 445e4ade).

---

## C. 면접 답변 스크립트용 (케이스화 부적합, 보존)

- **Viewer 토글 생명주기 desync** — 캐시된 THREE.Object3D 참조의 `.visible` mutation 잔존. 테스트 추가하려다 jsdom WebGL 한계로 "test-induced design damage"라 판단하고 **스스로 되돌려 E2E 가드로 결론** (세션 661f0154). "테스트보다 의도·최소화" 가치 실증.
- **cloud-mobile 썸네일 무한 재요청** (미완) — 캐시 부재+카드 remount 진단하다 상위 컴포넌트 재마운트로 좁혀가던 단계 (세션 4844afb7). ⚠️ "근본 해결 완료"로 쓰지 말 것.
- **Datadog 사내 스터디 발표** — Logs 주제를 가상 시나리오 대신 실제 prod 로그 실측으로 재구성 (세션 65f95c3b). 관측 도메인 전파·리더십 답변용.
- **GA4 가입 퍼널 계측 누락** — OAuth 가입 `account.signUp.complete` 누락을 이메일 경로와 비교 특정 (세션 9ad26ad4).

---

## D. 귀속 경계 주의 (작성 시 필수)

- **AEAD/envelope 암호화**(bf9ea37f, 4fdb8eb1) = 본인 학습·온보딩 문서만, 구현은 **BE팀**. 성과 인용 금지.
- **iwtk→Three.js 전환 본체** = 팀 AI 주도. 본인은 전환 후 부채 식별·회귀 추적·표준 매핑·가드만.
- **v6 Preference cutover(CRWN-3355)·crown iframe 호스팅 전환·모바일 viewport(DEN-4292/4293, hckim)** = 팀. 본인은 그로 인한 회귀 진단·fix만.
- 미완 항목(썸네일 등) "완료"로 쓰지 말 것.

---

## E. 추가 발굴 TODO — 9월~4월 (history 기반)

history.jsonl로 확인: 회사 작업 **2025-09-30 ~ 2026-06-08** (실질 요청 6,045개). 세션 raw는 주로 5~6월이라, **9월~4월 작업은 history 프롬프트 + git log + 회사 레포 코드로 발굴 필요**.
- 9월: caseInfoDialogue 공통 컴포넌트 리팩토링(FormProvider·Actions·Information 통합) — A1과 같은 결
- 3~4월: G 섹션 참조 (발굴 완료)
- 9~11월: 165 프롬프트로 적음 — 미발굴(추후 가볍게)

---

## F. 12~2월 발굴 (history 5,649 프롬프트 + jwkim 173커밋 교차) — 최대 작업 밀집 구간

### 신규 케이스급
- **F1. Electron 앱 개발용 Mock Server + 시뮬레이터 자작** ★ — Linker 개발에서 자동 업데이트·포트·CAM 설치 시나리오를 매번 재현하기 어려워 cam-mock-server를 만들고(12-12), 별도 레포라 불편해 `apps/linker-desktop/mock-server/`로 통합(12-29) + 웹 대시보드·시나리오·auto-update 시뮬레이션 구현. mock 코드가 `setupAutoUpdater` 본체 침투한 걸 스스로 분리 리팩토링(02-05). (출처: git ebcb4710 PR#30390) [✅ 본인] — Case 4 "재현 가능한 환경"의 데스크톱판.
- **F2. Datadog Feature Flag — render-props로 전환가능성 추상화** ★ — Protocol vs HTTP 방식을 훅 분기에서 "나중 전환 쉽게" render-props 컴포넌트 분기로 재설계(12-15). "피쳐 플래그니 공통 로직 중복 허용"이라는 의도적 비추상화. flag 다값 시 한계 스스로 제기. dev RUM 미설정 → 로컬스토리지 폴백(01-30). Suspense를 사용처 가깝게 이동해 race 수정(01-06). (출처: git 56254692 PR#28713·a4c6c8d9 PR#28715) [✅ 본인] — A1과 같은 "추상화 강도 의도적 선택".
- **F3. Windows EV 코드 서명 자동화 + 2-Stage 실험 후 직접 롤백** — SafeNet 토큰 수동 PIN 입력을 electron-builder make 타겟 자동 서명으로 전환(01-29, D1-2361). 빌드/서명 분리 2-Stage를 실험했으나 메모리 부족으로 병렬화 무의미 → yml·명령까지 직접 제거(git e02b2192 PR#30216, 02-03). [✅ 본인] — Case 2 "되돌린 결정"과 동일 결.

### 신규 에피소드
- **F4. batch-native 빌드 + variant 시스템 + 딥링크 프로토콜 통일** (git 30524·30612·30626) [✅]
- **F5. PNA(Private Network Access) 차단 해결** — batch-native 내장 Chromium의 HTTPS→localhost CORS를 PNA 비활성화로(git fe646016). LNA vs PNA 정책 차이까지 조사(12-13). [✅] — Case 1/11 자매편.
- **F6. imago-theme 디자인시스템 마이그레이션 롤백** — 롤백 계획서 직접 작성·실행(D1-2838, git 31228·31240·31251). [✅ 롤백 판단·실행만 / ⚠️ 마이그레이션 본체는 팀 hckim]
- **F7. Modeler Export v5 API 마이그레이션** — deprecated 제거 + productDisplayType 기반 Die/Base 분류(git 30509·30538). [✅]

### 기존 케이스 보강 (12~2월)
- **Case 1/11**: Linker 작업 최대 밀집 구간. Export Session 방식 Protocol Handler 신규(D1-1325/1349, git 28444) / **Protocol 좌표계 변환을 CAM 8종+로 확장**(MILLBOX·DGSHAPE·D+CAM·Alpha AI·PROCESS 등, git 29171~29205) = Case 1 "CAM 12종 단일 인터페이스"의 실제 구현 근거 / 삭제된 CAM 캐시 무효화·재설치 자동감지(DEN-4351).
- **Case 2**: F3·F4 직접 보강 + Self-hosted **좀비 Node 프로세스 정리 step**(git f43acc9 PR#31091) = 러너 상태 누적 장애 3번째 / 빌드 히스토리 기능(git 65433158 PR#31460).
- **Case 8 (결제)**: payment-client Unknown error = 환경변수 로딩(DEN-4330, git 30095) / Redis·Stripe 구독정보 불일치 디버깅 / Stripe 구독관리 스킬 자작(git f14b99 PR#30563).
- **Case 9 (관측)**: batch-web Datadog RUM + **dev 환경 요금 발생 방지**(git 727d551) → 이후 제거 = "관측 비용 통제" 판단.

> ⚠️ 정량은 PR 본문 미열람 — 인터뷰/PR diff로 별도 확인(임의 생성 금지).

---

## G. 3~4월 발굴 (history 3,917 프롬프트 + jwkim 171커밋 교차) — 4월이 최대(2,991)

### 신규 케이스급
- **G1. AI E2E 검증 파이프라인의 전신 — TC-Verify 스킬 자작 (Planner+Verifier)** ★ — 수백 qase TC를 사람이 검증하는 한계를 AI 에이전트로. v2→v3 직접 반복 설계, Planner+Verifier 2-에이전트, **Page Object를 YAML 산출**, tc-api.mjs로 qase API 연동. "서브에이전트 분리 vs 메인 직접 검증+PO 갱신"을 저울질하다 단순안 회귀. P009 Cloud 44 TC 자동 검증. (출처: git #5795·#5815·#5931·#32648) [✅ 단독] — **Case 5(AI 변경감지)의 직접 전신** — "처음부터가 아니라 TC-Verify에서 진화" 성장 서사.
- **G2. 세션/인증 모달 cross-app 공통화 — 근본은 idle tracker 즉시 시작** ★ — 미인증인데 inactivity 모달, 로그아웃 상태 cloud 진입 시 세션만료 무한 모달. 근본 = `AuthManager` 생성자가 로그인 무관 `IdleTracker.start()` 즉시 호출. 3단 방어(stopIdleTracking→noop subscribe→인증 라우트만 restart). 각 페이지 개별 모달 → "여러 페이지에서 열리니 공통 관리처로" 전환. 4앱(auth-core-sdk·account·dealer-backoffice·cloud) 일원화. (출처: git #5956·#5993·#6006) [✅] — Case 9·A1과 같은 "흩어진 fail mode를 공통 수렴" 시니어 패밀리.

### 신규 에피소드
- **G3. cloud-mesh-io 포팅 후 Construction Info XML 누락 — 동등성 검증으로 복구** ★ — HTTP Export zip에 CI XML 누락(crown-io→cloud-mesh-io 포팅 시 조용히 빠짐). `buildConstructionInfoFile.ts` 신규 + **crown-io 결과와 byte 동등성 검증**(L2 cross-shim 8시나리오×2path + L3 golden snapshot 테스트 인프라 자작). (출처: git #8142) [✅] — Case 1·6 결("포팅 부채를 동등성 테스트로 가드").
- **G4. SDS/SDVR 명세-검증 추적 체계** — 기획(SDS docx)과 검증(SDVR)이 따로 놀던 걸 md 변환·spec id 연결, 226 Viewer TC 전수분류. (출처: git #32525·#32568) [✅] — G1/Case 5 토대 또는 면접용.

### 기존 케이스 보강 (3~4월)
- **Case 2 (빌드)**: 4월 **Azure→self-hosted GHA 완전 이관**(Phase 0~4, PR #7576·#7689·#7861·#7884·#7929). NAS 직접 업로드 일원화, artifact ~757→348MB·Upload 114→50s(#7884), Developer Mode 심볼릭링크 권한(#7861) = 포트폴리오 "서명 권한 장벽" git 1차 출처. macOS 11일 번복 git: #32194(universal)→#32200(arm64)→#32612(intel). 04-28 "Azure 대비 현 파이프라인 결과 비교" 개선 측정 직접 수행. ⚠️ 웹 -90%/20→6분은 hckim, 데스크톱 GHA·NAS·artifact·universal+notarize는 본인.
- **Case 4 (격리)**: 4월 **My Designs/Flagged/Trash/Viewer Suite 수백 TC 격리 대이관**(D1-4837, Phase C~H). Seed 전용계정 세션격리(#6318, STATIC_PRO 동시세션 무효화를 별도 BrowserContext로)·Shallow-Clone(#6464). = Case 4 작업량의 실체.
- **Case 5**: G1(TC-Verify)이 직접 전신 — 회고에 "TC-Verify→변경감지 진화" 추가 시 성장 서사 완성.
- **Case 8 (결제)**: Stripe mock을 격리 docker 편입(04-20), 결제 E2E 시드 고민.
- **Case 9 (관측)**: 4월 ErrorBoundary 3-layer 5앱 통합 + GlobalErrorHandler + Datadog Taxonomy(D1-4763·4824·4827·4833) = Case 9의 핵심 작업 시기.

> ⚠️ 정량(artifact·시간)은 PR 본문 일부 확인(메모리 정량과 일치). 미확인 수치는 인터뷰/PR diff로.

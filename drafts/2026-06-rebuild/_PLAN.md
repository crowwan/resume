# 마스터 이력서 재설계 — 포지셔닝 + 카드 아키텍처 (2026-06)

> 입력: `findings/collected/_GAP-REPORT.md` + `_INTERVIEW-ANSWERS.md` + 영역별 raw(01~13) + interviews + WRITING-GUIDE + 4사 research.
> 원칙: 발굴 OK / 발명 NO. 귀속 룰 = 카드 단위 준수(타인 수치 배제, 본인 강점 격상). 정정으로 효율축 근거 전면 재구성.
> 이 문서는 **설계 청사진**(카드 골격·증거 매핑·귀속 가드)이지 본문이 아님. 본문 작성은 후속 단계.

---

## 0. 포지셔닝 (검증 결과)

### 우산 (1문장) — 1안 유지하되 효율축 근거 교체
> **"화면 너머 제품의 구조와 품질을 끝까지 책임지는 프론트엔드 — 웹↔데스크톱 경계, 모노레포 토대, 3D 렌더 품질, 관측 표준까지 직접 설계·운영한다."**

- 1안("제품의 구조와 품질을 끝까지 책임지는 FE")의 우산 문장은 **유효**. 정정 후에도 모든 강한 카드가 이 우산을 증명함(Linker=경계 설계, Solutions=토대, 3D 품질=품질 자동화, 관측성=품질 표준).
- 다만 기존 자기소개의 효율축 근거였던 **"웹 빌드 -90%/CI 3배/Electron 20분→6분"이 전부 무효**(웹 수치는 hckim 웹 트랙, '6분'은 미확정). → 효율축을 **Electron 빌드 실측(-56%/-59%) + 자작 visual regression metric + 격리 재현 + AI 변경감지 메커니즘**으로 **재구성**.

### 더 강한 포지셔닝 제안 (채택)
1안을 살리되, 정정으로 새로 드러난 두 고유 강점을 우산에 흡수:
- **"경계(boundary)를 설계하는 FE"** — 웹↔로컬 디바이스(Linker/LNA), 부모↔팝업 origin 격리(결제), 두 렌더엔진 책임경계(VTK/Three), EB↔Datadog 책임분리. → **토스플레이스·라인의 webview/native 경계 서사와 1:1**. 이걸 "구조" 축에 강하게 묶는다.
- **"자기 결정의 후과까지 추적하는 FE"** — 자작 Taxonomy 자기폐기, gamma 0.29 자기폐기, Rspack→Vite 수렴, blur 휴리스틱 부채 추적, VTK 통합 보류 ADR. → "판단" 축의 미들→시니어 신호. 채널톡 "왜 이렇게?" 컬처 직격.

### 축 (4축 — 유지, 근거만 재정렬)
| 축 | 정정 후 근거 (요지) |
|---|---|
| **범위(scope)** | FE + Electron 데스크톱(빌드·서명·배포 인프라 단독 운영) + 일부 BE 풀스택(랜딩 Fastify/Mongo) + 크론/EC2 운영 |
| **구조(structure)** | urlHelper 토대 → 팀 runtime config·격리 / Git Subtree 모노레포 이관 / MFE iframe·MF / 3D 렌더 SoT 통합 / 3-layer EB / 경계 설계(LNA·origin 격리·렌더엔진 책임경계) |
| **판단(judgment)** | LNA 20여 방안 분석 / VTK 통합 보류 ADR(실측으로 '안 함' 정당화) / NX 유지(전환비용<이득) / 자기폐기 서사(Taxonomy·gamma·Rspack) / blue-green green검증 보류 |
| **효율(efficiency)** | ★재구성: Electron 빌드 -56%/artifact -56%/upload -59% / 자작 mesh visual regression metric(Playwright 한계 우회) / 커밋단위 격리 재현 / AI 변경감지(diff→연관 qase TC 선별→실행→Teams+triage) |

---

## 1. 카드 아키텍처 (강한 순) — career-description 6~8개

> 통합·분리·취사선택 결정 로그는 §3. 각 카드 증거·귀속 가드는 StructuredOutput `cards`에 상세.

| # | 카드 | 주축 | resume 등재 | 한 줄 |
|---|------|------|:---:|------|
| 1 | **Linker — 웹↔로컬 CAM 디바이스 브리지** | 판단/구조 | ✅ | LNA 규제 하 브라우저↔로컬 CAM 연결을 Custom Protocol로 재설계, 12종 외부 CAM 추상화 |
| 2 | **Solutions 플랫폼 토대 — urlHelper·모노레포·격리 재현** | 구조 | ✅ | 도메인 통합 라이브러리로 runtime config·격리환경의 토대를 깔고, Git Subtree로 분산 앱을 NX로 통합 |
| 3 | **3D 렌더 품질 자동화 — 정공법 재설계 + 자작 visual regression** (★신규) | 효율/판단 | ✅ | 색·조명을 SRGB 정공법으로 재설계(~1,420줄 제거)하고, Playwright가 못 잡는 mesh 회귀를 자작 metric으로 차단 |
| 4 | **품질 자동화 토대 — 격리 재현·E2E·AI 변경감지** | 효율 | ✅ | 커밋단위 결정론적 재현 환경 + 만성 red 근본수정 + AI가 diff 보고 연관 TC 선별·실행 |
| 5 | **관측성·ErrorBoundary 표준 정렬** (★신규) | 구조/판단 | ✅(택1) | 5앱 3-layer EB를 팀 Datadog 표준에 정렬, 자기 분류를 스스로 폐기한 종합 계획 author |
| 6 | **Electron 빌드·배포 인프라** | 범위/효율 | (career) | 로컬 PC 빌드 → self-hosted 빌드머신·에이전트 풀 단독 운영, macOS 빌드 -56% |
| 7 | **Account — B2B 구독·결제 FE** | 범위 | (career) | 크레딧→구독 전환 FE 전담, 부모↔팝업 origin 격리 결제, 정확성 검증 구조(제품 수치 0) |
| 8 | **MFE 공통모듈 + 랜딩 풀스택** (압축 병합/후미) | 구조/범위 | (career 후미) | iframe↔빌드타임↔iframe 회귀 재선택 / 랜딩 v3 단독(약 90%)+타입세이프 i18n+3계층 풀스택 |

- **resume.md 등재(강한 5개)**: 1 Linker · 2 Solutions토대 · 3 3D품질 · 4 품질자동화 · 5 관측성(또는 6 Electron 중 JD에 따라 택1).
- **career 후미 축소**: Batch(빌드는 6으로, 본체는 한 줄) · Design System(DEN-548 한 줄) · 랜딩은 8에 병합.

---

## 2. 카드별 핵심 증거 + 귀속 가드 (요지)

### 카드1 — Linker (판단/구조)
- 증거: LNA→Custom Protocol 재설계 / 20여 방안 분석(WebSocket "곧 LNA 적용" 탈락, HTTPS 로컬서버 "네트워크 위치 기반"이라 무효) / **12종** 외부 CAM 좌표계·전달 추상화 / 특성화 테스트로 스펙 고정 / 데이터채널 해결·감지채널 부채 2채널 프레임 / 본인 Jira ≥50건(2026-01~06 운영) / D1-1824 Datadog 연동.
- 지속성·한계: blur 휴리스틱은 **현재 머지된 상태, 개선 검토 중**(해결 성과 아님). 양방향성 포기 트레이드오프 → 감지 채널 부채.
- 귀속 가드: CAM '16'→**12종**. 초기 검증 타인/프로덕션화 본인. "회귀 N건 잡음" 류 단정 금지.

### 카드2 — Solutions 토대 (구조)
- 증거: **urlHelper 도메인 통합 라이브러리(본인 토대)** → runtime config·격리환경의 시작점 / Git Subtree Split 이관(앱2+libs6 → 메인 NX) + `git subtree pull` 동기화 + 네임스페이스 리네임 / i18n 스크립트 중앙화(본인 자작 문서) / non-buildable artifact 제거로 NX 태스크 11→9 / NX 유지 판단(전환비용<이득).
- 귀속 가드: runtime config 자체=**팀 공동**("주도/공동설계" 금지). 웹 -90%/CI 3배/promote/cloud-desktop 6→1.5분 수치 **전부 금지**(hckim). Rspack 단독주도 금지(getPublicPath util만 확정).

### 카드3 — 3D 렌더 품질 자동화 (효율/판단) ★신규
- 증거: 색/조명 **SRGB 정공법 재설계**(gamma 0.29는 본인 폐기한 중간단계) / **~1,420줄 제거**(GENERIC UV draco, main-thread WASM 540줄 폐기, PR#9432) / **자작 mesh visual regression metric**(pngjs mean‖Δ‖ compareMeshRender, swiftshader로 CI/local GPU차 흡수, 12 metric baseline, GREEN 0.0 vs RED 2.2865 FAIL — PR#9517/#9655) / viewer mesh SoT 통합 ADR / **VTK 통합 보류 ADR**(VTK 306파일·iwtk 649파일 실측으로 "번들 감소 0" 증명 → '안 함' 정당화).
- 귀속 가드: iwtk→Three "전환" 번들 -57%/-68MB = **팀-hckim**(본인은 그 위 레이어). 번들 감소 정량 인용 금지. PR 라인수는 Azure 머지라 gh 검증 불가 → "커밋/vault 기재값" 톤, 추정 금지.

### 카드4 — 품질 자동화 토대 (효율)
- 증거: 커밋단위 클라+서버 격리 재현(결정론적·다른 변경 간섭 없이 재현) / 만성 red 4-root 진단 + 본인 근본수정(#10759 prefetch-frontends, #10783 billtap 404 ~28→0·passed 5→14) / E2E 모노레포 통합·Page Object·세션 자동관리 / K8s→EC2 비용 EKS~$150→EC2~$30/월 / **AI 변경감지**(커밋 diff→변경파일 분석→Claude가 qase TC 중 연관 TC 선별→실행→Teams 보고 + triage 실제회귀/테스트코드 구분; Teams 실물 증거 존재).
- 지속성·한계: 격리 컨테이너 무거워짐→성능 개선·재현용/수정용 이미지 분리 검토 중(담백하게). AI 변경감지 2026-05-28부터 daily 실패 누적→현재 미사용, local-daily 재활성 전환.
- 귀속 가드: "환경 차이로 안 보이던 버그" 프레임 폐기→"다른 변경 간섭 없이 결정론적 재현". 격리 위키 문서는 hckim, 본인은 티켓 실행+근본수정. "AI가 회귀 N건 잡음" 금지(메커니즘+triage로만). 테스트 실행시간(🔸)은 제품 성과로 포장 금지.

### 카드5 — 관측성·EB 표준 (구조/판단) ★신규
- 증거: **EB→Datadog 종합 계획서 author=본인** / 3-layer EB(Root→Section→Feature) D1-4763 epic + **5앱 통합** / **Taxonomy 7분류 자기폐기**(특정앱 편향→팀 범용 표준에 정렬) / zero-dependency RootFallback 원칙 / Export 실패 가시성 ADR(DEN-4571, RUM 124건 collapse 진단) / Dogtap 격리 회귀가드.
- 귀속 가드: 관측 attribute **어휘 정의는 hckim**, 본인은 EB lib을 그 어휘에 **align**("표준 어휘를 만들었다" 금지). 마이그 전후 triage 시간 단축은 문서상 **목표값**(실측 아님)→수치 금지. _service-overview §4 [팀] 오기 정정 반영.

### 카드6 — Electron 빌드·배포 인프라 (범위/효율)
- 증거: 로컬 PC 빌드→self-hosted 빌드머신 / **Windows 코드서명 self-hosted 빌드머신+에이전트(SigningAgent02) 풀 직접 운영** / macOS 빌드 **33~39분→17~24분(-56%)**, artifact 757→334MB(-56%), Upload 114→47초(-59%) / blue-green green검증 운영비 2배 대비 가치로 의도적 보류 / macOS arch 3연속 번복(11일) 판단 서사 / 계획서 선행 운영 습관.
- 귀속 가드: '6분' 미확정→PR 실측(-56%/-59%)으로 대체. /tmp 장애 수치는 8앱 공유 호스트→Electron 단독 환산 금지. cloud-desktop CI 6→1.5분(hckim 웹)과 **혼동 절대 금지**.

### 카드7 — Account 구독·결제 FE (범위)
- 증거: 크레딧→구독 워크플로우 전담(플랜·시트·취소/재개·쿠폰·결제수단) / **별도 결제 팝업앱에 Stripe 결제페이지 추가**(부모↔팝업 origin 격리, SUB-331/387) / 비즈니스 에러 코드별 분기 + Fault Tolerance / 폴링 안전장치(무한폴링→base 3000ms·최대 20회) / BO 구독 E2E 종단 / FSD 과적용 학습.
- 귀속 가드: **Account 제품 지표 없음**(사용자 확인). -39%/-80%는 **테스트 실행시간**일 뿐 제품 성과 아님→제품 수치 창작 금지. "결제 앱 본인 구현"→"기존 팝업앱에 Stripe 결제페이지 추가"가 맞음. Stripe Connect/서버 설계=팀-Adam.

### 카드8 — MFE + 랜딩 (구조/범위, 압축 병합)
- MFE: iframe 런타임 통합→빌드타임→iframe 회귀 재선택("정답 기술 없이 조직·배포 제약에 맞춰") / MF는 써보고 이 건엔 제외(다른 서비스 적용).
- 랜딩: **v3 단독성 383 vs 24(약 90%, 16배)** / 타입세이프 i18n(없는 키 컴파일타임 차단, 영어 단일소스→534줄 자동생성, 34파일) / 3계층 CRUD 풀스택 1주(Fastify PATCH + Mongoose updateMany) / v3.0.x 47태그 2년+ 운영.
- 귀속 가드: GA4 택소노미/전환 설계=hckim(랜딩 본인 기여 아님). MUI 토큰/디자인시스템 마이그=hckim(본인은 단일 컴포넌트 수정·롤백).

---

## 3. 통합·분리·취사선택 결정 로그

1. **3D 품질을 MFE에서 분리해 독립 카드 승격(신규)** — 정정으로 본인 고유 강점(SRGB 정공법·1,420줄 제거·자작 visual regression)이 드러남. 기존 MFE 카드의 "렌더 정합 수정 담당" 한 줄은 본인 깊이를 심하게 과소평가. iwtk→Three 전환 골격(팀)과 분리해 "그 위 레이어"만 본인 카드로.
2. **관측성을 Account에서 분리해 독립 카드 승격(신규)** — 종합 계획서 author=본인 확정. 기존 Account의 "ErrorBoundary 이관" 한 줄을 카드로. 단 JD에 따라 resume에선 Electron(6)과 택1(공간).
3. **격리 재현 + E2E + AI 변경감지를 한 카드(4)로 유지** — 셋 다 "품질 자동화 토대"로 응집. 단 AI 변경감지는 메커니즘+한계로만, 격리는 의도 정정 반영.
4. **Linker를 1순위로** — 4사 union에서 가장 강한 매칭(토스플레이스 webview↔native·시리얼포트 디바이스 브리지 1:1, 라인 webview, 채널톡 데스크앱). 판단 깊이(20여 방안·2채널 프레임)도 최상.
5. **Electron 빌드를 Linker/Batch 공통 인프라 카드(6)로 통합 유지** — 단 코드서명·blue-green·자동업데이트를 여기로 모으고 Linker(1)는 브리지 아키텍처에 집중.
6. **Batch 본체·Design System은 career 후미 축소** — 타겟 연관도·임팩트 낮음(WRITING-GUIDE §1-1). 빌드는 6으로, Batch v1 본체는 한 줄, DEN-548은 한 줄.
7. **랜딩은 MFE와 병합(8)** — 둘 다 단독으로 첫 화면 매력 약함. 단 랜딩의 단독성(약 90%)·타입세이프 i18n·풀스택은 "범위" 축 증거로 보존.

---

## 4. 회사 렌즈 union 체크 (마스터가 품는가)

| 회사 | 핵심 요구 | 품는 카드 |
|---|---|---|
| 라인 Pay | 모노레포·PR격리 E2E·AI dev·글로벌 결제·webview | 2,4 / 4 / 4 / 7 / 1 |
| 오늘의집 | 품질자동화·CI/CD·플랫폼(MSA FE 분리)·LLM·구독 | 4,6 / 2(Git Subtree=오늘의집 MSA 분리와 동결) / 4 / 7 |
| 채널톡 | 모노레포(왜 이렇게?)·데스크앱·DX 빌드자동화·디자인시스템·AI | 2(NX 유지 판단) / 1,6 / 6 / 8 / 4 |
| 토스플레이스 | webview↔native·시리얼포트 디바이스·Electron·MFE·추상화·DRI | **1(1:1)** / 6 / 8 / 1(12종 추상화) |

→ 마스터 우산 + 8카드가 4사 union을 모두 커버. **Linker(1)가 토스플레이스 1:1 + 라인 webview, Solutions토대(2)가 채널톡/오늘의집 모노레포, 품질자동화(4)가 전사 공통.**

---

## 5. 다음 단계
1. 이 청사진으로 `career-description.md` 카드 골격 작성(WRITING-GUIDE §3 압축 포맷).
2. resume.md 강한 5카드 압축 등재.
3. 평가 서브에이전트 루프(EVALUATION-RUBRIC) → 통과까지 반복. 포지셔닝도 루프에서 재검증.
4. 두 문서 기간·수치·명칭 일관성 점검 + 귀속 가드 최종 검수(타인 수치 0건 확인).

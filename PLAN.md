# Resume Repo — 작업 계획서

> 1년 내 이직(라인·오늘의집·채널톡·토스플레이스) 기준. **1차 자료 발굴 → 귀속 정정 → 담백 재작성 → 회사별 변형**으로 운영.
>
> 운영 룰: [CLAUDE.md](./CLAUDE.md) · 작성 기준: [WRITING-GUIDE.md](./WRITING-GUIDE.md) · 평가: [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md) · 발굴 룰: [findings/README.md](./findings/README.md)

---

## 현재 상태 (2026-06-28)

**Phase: 마스터·파생 구조 개편 완료 + 에이블리 추가. 포트폴리오를 경력기술서로 강화(케이스별 정량 성과 + 개요→문제 흡수), 이력서 핵심역량 제거(자기소개·프로젝트와 중복), 전체 H1 `Frontend Engineer`로 통일. 다음 = 라인·채널톡·토스플레이스 portfolio 파생 + 미해결 확인 3건.**

**문서 아키텍처 (2축, 확정)**
- `resume.md` (루트) → 담백 마스터 이력서 — ✅ 자기소개 2단(캐치프레이즈+성향·문화), **핵심역량 제거**, 경력·프로젝트·기술스택·교육
- `portfolio.md` (루트) → 서사 케이스 11개 — ✅ **케이스별 '성과' 한 줄 + 개요 흡수**(문제/해결/회고), About 1문단 압축, 코드블럭 제거
- `archive/career-description.md` → 보존 (빌드·index 제외) — ✅
- `applications/README.md` → **JD 파생 체크리스트** (오늘의집 탈락 분석에서 도출) — ✅
- `applications/*/` → 회사별 파생: **오늘의집·당근·에이블리 완비**(resume+portfolio+research) / 라인(answers형)·채널톡·토스플레이스(resume만) — 🔶 portfolio 3사 미완
- 볼트 사본: `~/work-vault/career/applications/` — 파생 6사 면접준비용 미러(정본은 repo)

원칙: resume는 "무엇을 했나(담백)", portfolio는 "왜·어떻게·무엇을 배웠나(서사) + 성과". JD 변형은 **내용 재작성이 아니라 Case 선별·순서·강조(큐레이션)**. 파생은 마스터에 있는 사실만, 새 수치 발명 금지. H1 = `Frontend Engineer`(이력서)·`포트폴리오 | Frontend Engineer`(포폴) 통일. html은 pandoc `-M pagetitle`(제목 중복 방지)·`-c <css>`로 변환.

**핵심 산출물 위치**
- 이력서: `resume.md`(루트, Pages 자동배포) + `applications/{라인,오늘의집,채널톡,토스플레이스}/`
- 포트폴리오: `portfolio.md`(루트) · 전용 스타일 `templates/portfolio-style.css` · 다이어그램 `templates/mermaid.html`
- ★ **발굴 백로그**: `findings/portfolio-case-backlog.md` — 2025-09~2026-06 전 구간, 신규 케이스 후보 ~18개 + 기존 11케이스 보강 + 귀속주의
- 귀속 경계 메모리: `resume-attribution-boundary` · 코드사인 미도입: `resume-codesign-build-integration-not-adopted` · 2축/추상화 원칙: `resume-architecture-and-card-abstraction`
- 발굴 raw: `findings/collected/` (+ `_GAP-REPORT.md`, `_INTERVIEW-ANSWERS.md`)

---

## 실제 진행한 파이프라인 (이번 재작성)

```
1. 대규모 자료 수집   본인(jwkim) 커밋·PR·Confluence·Jira·vault·S3 전수 발굴 → findings/collected/
      ↓
2. 귀속 정정         git/Confluence author 교차검증 → 오귀속 제거·누락 강점 격상 (_GAP-REPORT)
      ↓
3. 발명 방지 인터뷰   사용자만 아는 사실·수치·기여 확인 (_INTERVIEW-ANSWERS)
      ↓
4. 보이스 리서치      한국 IT 컨벤션 밖 사례 수집 → 담백 스타일 확정 (RESEARCH-voice-format-board)
      ↓
5. 담백 재작성        최종 스타일로 마스터 8카드 + career/resume (final/)
      ↓
6. 회사별 변형        4사 렌즈로 재정렬 + 담백한 회사-맞춤 도입부 (final/variants)
      ↓
7. 회사별 평가        RUBRIC + 귀속·tic 점검 (통과까지)
```

핵심 원칙은 그대로: 사실·수치는 **발굴 OK / 발명 NO** (CLAUDE.md §3). 본인 기여 경계는 메모리 `resume-attribution-boundary` 참조.

---

## 이번 세션 (2026-06-08~09) 완료

| 작업 | 상태 |
|------|------|
| resume 마스터 6카드 담백화 (판단 중심 + 사실 정정: 크론잡·익명화·코드사인 미도입·공존X 등) | ✅ |
| career-description → `archive/` (빌드·index 제외) | ✅ |
| resume 변형 4사 담백화 (오늘의집·채널톡·토스·라인) | ✅ |
| portfolio 11케이스 전면 재작성 (PDF 형식, reviewer 2회 반영) | ✅ |
| portfolio 전용 스타일 `portfolio-style.css` + mermaid 테마 | ✅ |
| 메모리 2건 박제 (코드사인 미도입 · 2축/추상화 원칙) | ✅ |
| 대규모 발굴 → `findings/portfolio-case-backlog.md` (전 구간, ~18 신규후보) | ✅ |

## 이번 세션 (2026-06-09 후속 — 코드·표지 반영) 완료

| 작업 | 상태 |
|------|------|
| portfolio 💻 5개 실제 코드 발췌·박음 — **전부 본인(jwkim/crowwan) author git 검증** | ✅ |
| ↳ Case1 export 특성화테스트(`92d99f1`) · Case3 urlHelper(`8416894`/`70ab668`) · Case6 dental.ts displacementMap(`50533d3`) · Case10 타입세이프 i18n(`8075dce05`/`de091a574`, 레포 `landing-page-client`) | ✅ |
| ↳ Case8 errorClassifier(`6f0862c`)는 결제 아닌 **Case9(관측)로 이동**. 결제 HTTP200 분기는 hckim 코드라 코드 제외(서사만) | ✅ |
| 🖼️ 표지 이미지 — 프로덕션 풀아치 스캔(환자정보·UI라벨 제외 크롭) `images/portfolio/cover-scan.png` | ✅ |

> ★ **귀속 검증 핵심**: 1차 발굴 시 git blame이 hckim으로 나온 코드 다수 → 본인 author 커밋(`git log --author`)으로 재발굴해 **본인이 실제 작성한 라인만** 박음. Case8 결제 핵심 로직은 본인 author 없어 코드 미게재(오귀속 방지).
> ★ **이미지 캡처법(재사용)**: claude-in-chrome은 원격세션이라 `save_to_disk` 불가 + Dentbird 단일세션 정책상 Playwright와 공존X. → **Playwright headed + persistent profile**(`/tmp/pw-dentbird-profile`)로 사용자가 1회 직접 로그인 후, 같은 프로필로 크롭 캡처. `NODE_PATH=~/Works/devops/dentbird-solutions/node_modules`. 프로덕션은 실데이터라 3D 캔버스만 clip(환자정보 패널 제외).

## 이번 세션 (2026-06-11 — 전수 리뷰·병합·시각화) 완료

| 작업 | 상태 |
|------|------|
| **4소스 전수 교차검증** (레포 findings · vault · 회사 git/코드 · Confluence/Jira) — 에이전트 4기 병렬 | ✅ |
| 사실 정정: 격리 시행착오 16→**17건**(vault) · 빌드 36→20분 → **33~39분→17~24분 -56%·아티팩트 757→334MB**(PR 실측) · urlHelper 발췌 2곳 실코드 교정(`_URL`·`dentbird.com`) · billtap 28건 **→0 달성** 반영 · "3-layer 5앱" → 체계 설계+표준 정렬(깊이 앱별)로 정밀화 | ✅ |
| **Case 6 VTK 보류 supersede 반영** — 6/11 전제 변화(서버 오프로드)로 팀(hckim)이 Three 통합 → "뒤집힌 전제" 서사로 흡수 | ✅ |
| 발굴 사실 추가: CAM 12종 구성(프로세스 8+포트 4, 코드 실증) · 2단계 방향 **본인 제안**(CP-1327 코멘트 author) · 관측 Before/After 격리 검증(Confluence 본인 author) · OAuth 콜백 race 트리 밖 분리(2-PR) · TC-Verify→AI 변경 감지 진화 · DNS 포화 진단·오진 반증 · 시각회귀 대기 120→37.7초 · 분류기 공통 lib 승격 | ✅ |
| **케이스 병합 11→9**: Case 11(딥링크 감지)→Case 1로, Case 5(AI 변경 감지)→Case 4로 흡수. 번호 재정렬·상호참조 갱신 | ✅ |
| **SVG 다이어그램 10종** 제작(`images/portfolio/diagrams/`) — mermaid 전부 교체, 레이어드(격리 스택·MFE·관측)는 타깃 이미지 스타일. Chrome 렌더 검수 완료 | ✅ |
| 빌드 결함 수정: deploy-pages.yml에 `cp -r images output/` 추가 (기존엔 표지/다이어그램 미배포) + `.diagram`/figure CSS | ✅ |
| 변형 동기화: 토스플레이스·채널톡 resume.md/html 빌드 수치 실측으로 교체 | ✅ |

## 이번 세션 (2026-06-11 후속 — 프론트엔드 색 재편) 완료

> 사용자 피드백: "프론트엔드 개발자라고 하기엔 애매하다. 분석 정보에 FE 역량 있을 것" → 진단 결과 **자료는 충분, 인프라 프레임으로 분류해 FE 색을 못 살린 게 문제**.

| 작업 | 상태 |
|------|------|
| **FE 역량 4소스 재발굴** (회사코드 git·vault·interviews/04·컨플루언스 본인글) — 에이전트 병렬 | ✅ |
| ↳ 컨플루언스 **본인 작성 FE 설계글 25편+** 발굴(선언적 에러처리·Compound/RenderProps·클린아키텍처+DI·FSD·도메인모델링·추상화 절제·서버/클라 상태분리·MF 트레이드오프·@imago/feedback-react) | ✅ |
| ↳ 회사코드 신규: 썸네일 lazy-loading 배치훅·Viewer partial render·URL↔state race·Snackbar Portal·커스텀훅 추출·viewer GPU 라이프사이클 | ✅ |
| **신규 FE 케이스 3개**: Case1 디자인패턴·도입의 전제(FSD+Compound/RenderProps+CaseInfo 66%↓+성장서사) · Case2 리스트 렌더링 성능·URL 상태 · Case3 Viewer partial render | ✅ |
| **순서 재배치**: FE 본연(1-3) → 에러·복원력 3부작(partial·결제·관측 3-5) → 3D·랜딩(6-7) → 플랫폼·인프라(8-12). 상호참조 갱신 | ✅ |
| About·Skills·표지 인용 FE 중심 재편 (Frontend·아키텍처·설계를 앞에) | ✅ |
| resume.md 핵심역량·주요프로젝트 FE 우선 재편(프론트엔드 설계·화면/상태/성능 추가) | ✅ |
| 신규 SVG 다이어그램 3개(design-patterns·list-performance·partial-render) 제작·렌더 검수 | ✅ |

> ★ **귀속·발명 주의 (재발굴에서 확인)**: ① 썸네일 "90%↓" 등은 **자기보고 커밋 메시지** → 수치 미인용(서사만). ② CaseInfo 66%↓는 본인 컨플루언스 회고 수치 → "본인 회고 기준" 명시해 사용. ③ 2025-10 이후 일부 커밋 **AI 페어(Co-Authored-By Claude)** → 설계·판단은 본인이나 면접 시 정직 표기. ④ 순수 3D 카메라/raycast 직접 구현은 근거 없음 → 안 씀(뷰어는 "표시·상태·에러" 측면만).

## 이번 구간 (2026-06-13~14 — 이미지·시각화·케이스 서사 재점검) 완료

| 작업 | 상태 |
|------|------|
| **Figma MCP 연동** → 사내 디자인을 포트폴리오 참고 이미지로 도입 (`images/portfolio/screenshots`) | ✅ |
| 표지 이미지 교체 — 스캔 파일 → **케이스 목록 화면**. Linker·Mock Server 이미지 그리드 합치고 전체 이미지 크기 축소 | ✅ |
| SVG 다이어그램 레이어 박스 이탈 수정 · 코드/스킬 영역 가독성 개선 | ✅ |
| **Skills 섹션 재편** — 현업서 확인하는 스킬만 남기고 표+칩+기술 아이콘(react·tanstack 등) 형태로 | ✅ |
| **사실 정정**: 부분 실패·결제 fault tolerance는 **본인 제안 단계**(착수 X)로 명시. Case 3(가시성)을 에러 처리로 흡수 | ✅ |
| collected 재점검 → **피쳐 플래그·BO E2E** 보강 후보 식별(팀이 적극 도입 중) | ✅ |
| 케이스별 서사 재점검 착수 — Case1 FSD·디자인패턴(도입 목적 불명확의 학습 서사), Case2 썸네일, Case3 이중 에러 | ✅ |

## 이번 구간 (2026-06-15~20 — 케이스 형식 통일·오늘의집 지원·코드블럭 제거) 완료

| 작업 | 상태 |
|------|------|
| **전 케이스 형식 통일** — 개요/문제/해결/회고. 제목도 주제 중심 정정 | ✅ |
| Case2 정정 — TTL·무한요청 해결을 **상태관리 오류(URL 쿼리+훅 이중관리, 파생상태 문제)** 방향으로 재편(Batch web 뷰모드 전환 버그) | ✅ |
| Case3 — **예측 가능/불가능 에러 미구분** 문제의식 중심으로 재편, Case4(에러 처리 주도 개선)와 연결 | ✅ |
| **오늘의집 지원 파생** — JD 매칭 5케이스 큐레이션 + resume·portfolio·html 변환본 + research | ✅ |
| **자기소개/About 재작성** — 전체 Claude 세션 분석으로 성향 도출(근본원인·방향공유·트레이드오프 측정). 인용구를 About에 통합 | ✅ |
| ★ **코드블럭 전부 제거** (사내 자산 보호) — portfolio 코드 발췌 → 서사로 | ✅ |
| 기술스택 정정 — Emotion→MUI, MFE 표기, Vite·Webpack 대표만, 백엔드 제거. 카드별 기술줄 제거(스택 비슷해지는 문제) | ✅ |
| **AI 활용 역량 추가**(세션 기반, 멀티에이전트 제외) · d1 개발팀 도구(TC 검증 대시보드) 추가 · 분류기 shape 판별 설명 보강 · 회고 살 붙이기 | ✅ |

## 이번 구간 (2026-06-21 — 오늘의집 탈락 분석·전략 전환·당근 지원) 완료

> ★ **분기점: 오늘의집 서류 탈락.** 분석 → ① 파생이 마스터보다 길어짐(압축 실패) ② 포트폴리오 5장이 인프라 4:제품 1 → "인프라 FE" 인상 강화 ③ "보완하고 싶은 영역" 자백이 첫 컷에서 미달 신호로 읽힘.

| 작업 | 상태 |
|------|------|
| **전략 전환** — 마스터=풀 카탈로그(전부 포함) / 파생=JD에 맞게 선별·압축·재배치. `applications/README.md`에 **JD 파생 체크리스트** 박제 | ✅ |
| ★ **포지셔닝 전환** — "코드가 오래 버티도록(longevity)" 폐기 → **"빠른 변화(AI 기반) 속 안전한 개발"**. 메모리 `resume-positioning-safe-change` 박제 | ✅ |
| 마스터 카드 전수 리뷰 · 제목 프로젝트 기반화·기호(+·-) 제거·전문 톤 · 테스트 카드 "가드" 같은 AI 표현 → 업계 보편 용어로(룰에도 반영) | ✅ |
| 이력서 **경력 섹션 신설** — 카드 사이 divider + 회사/기간/담당업무/프로젝트 구조 | ✅ |
| **당근 지원 파생** — content·platform 공고 리서치(vault) → 커머스 직무로 결정, 5케이스 큐레이션(resume+portfolio+research+interview-notes) | ✅ |
| 지원서에서 포트폴리오 **링크 제거**(PDF라 클릭 불가) | ✅ |

## 이번 구간 (2026-06-22~28 — 자기소개 재작성·에이블리 추가·구조 개편·통일) 완료

> ★ 자기소개를 **2단 구조(캐치프레이즈+본문)**로 전면 재작성하고, 전 Claude 세션(dentbird 7.7만 발화) 분석으로 성향을 실증해 반영. 포트폴리오를 경력기술서로 강화.

| 작업 | 상태 |
|------|------|
| **자기소개 2단 재작성** — 캐치프레이즈("AI로 빠르게 개발하며 검증으로 안전하게") + 근거(결제 28→0 등)·효과측정·정직·협업문화 흡수. 마스터·전 파생 | ✅ |
| **세션 전수 분석**(solutions/solutions2/3·vault·blog) — 근본원인 집착·검증 재현·트레이드오프 측정·자기작업 재평가(정직) 실증. 약점(brute-force 재발·머지 속도·툴링 짜증)도 발굴 | ✅ |
| **에이블리 추가** — JD·컬처 리서치(`applications/에이블리/research.md`) + 파생 세트(resume·portfolio·html). 5케이스 "제품 먼저" 큐레이션, H1 Frontend Engineer | ✅ |
| **포트폴리오 케이스별 '성과' 한 줄** — 검증 수치만(28→0·-56%·120→37.7초·124건·11→9·CAM12종 등). CaseInfo 66%는 꼬리표 없이, AI 선별 정량효과는 제외(측정근거 없음). 마스터·당근·에이블리 | ✅ |
| **개요 → 문제 흡수** — 전 케이스에서 `### 개요` 제거하고 맥락만 문제 첫 문장으로. 상단 종합 성과 섹션은 폐기(케이스별로 분산) | ✅ |
| **About 1문단 압축** — 포폴 About 4문단 → 소개 1문단(성향·문화는 이력서로 이전) | ✅ |
| **이력서 핵심역량 제거** — 자기소개·프로젝트·기술스택과 중복 → 6사 전부 삭제 | ✅ |
| **H1 통일** — 이력서 `Frontend Engineer`·포폴 `포트폴리오 \| Frontend Engineer`. pandoc `-M pagetitle`로 본문 제목 중복 해소, 파생 html 재생성 | ✅ |
| **볼트 사본 갱신** — `~/work-vault/career/applications/` 6사(당근·에이블리·오늘의집·채널톡·토스 최신화) | ✅ |

## 다음 세션 할 일 (우선순위)

| # | 작업 | 메모 |
|---|------|------|
| 1 | **라인·채널톡·토스플레이스 portfolio 파생** | `applications/README.md` 체크리스트 따라 — JD 매핑 → 5케이스 선별(제품 카드 1~2장 의무) → 순서·톤. 오늘의집·당근이 레퍼런스. 라인은 answers형이라 형식 확인 필요 |
| 2 | **라인 resume.md 본체** | 현재 `answers.md`+style만 있고 이력서 본체 없음 — 라인은 답변형으로 가는지/별도 이력서 낼지 사용자 확인 |
| 3 | **미해결 확인 3건 처리** | 아래 "미해결 확인사항" — Case i18n 프로덕션 반영 여부 · "격리 환경 직접 구축" 경계 · CAM 12종 표현 (발명 방지) |
| 4 | (선택) **백로그 케이스 승격** | `findings/portfolio-case-backlog.md` — Feature Flag·Mock Server·TC-Verify 등. 마스터 11케이스로 안정이라 우선순위 낮음. 승격 시 git author 검증 필수 |

> ※ 이전 PLAN의 "이미지 플레이스홀더 2개" 항목은 6/13~14 Figma 도입으로 해소됨(플레이스홀더 0개). "케이스 9개" 표기도 현재 11개 기준으로 정정.

## 미해결 확인사항 (사용자 답 필요 — 발명 방지)

- **Case 9(랜딩) 타입세이프 i18n의 프로덕션 반영 여부** — git 검증 결과 코드는 본인 author로 실재하나 `test/i18next-typescript` 브랜치에만 있고 kevin/main 계보에 병합 흔적이 없음(로컬 클론이 2025-11에서 stale, 원격 fetch는 인증 불가). 실제로 배포까지 갔는지 확인 필요 — 아니라면 "전환했습니다" 표현 조정
- **CAM 12종 구성 표현** — 코드 enum(PROCESS 8종·PORT 4종)을 "프로세스 실행 방식 8종 + 포트 연동 방식 4종"으로 적음(`portfolio.md` Case 1). 이 의미 해석이 맞는지 확인
- **격리 재현 환경 "직접 구축"** — Confluence의 격리 환경 문서 2건 author가 김현철. 본인 "직접 구축" 표현(Case 4 결과·resume)이 면접에서 방어 가능한 경계인지 확인 (현행 유지 중)

### 해결됨 (2026-06-11 사용자 확인)

- ✅ **Case 1 "핵심 경로에서 분리"** → 모호한 작문이라 정정. 실제 방향 = **설치 감지 판정 제거 + 다운로드 안내 항상 제공**(기획 합의, 착수 미정). 근거: vault `inbox/2026-06-08-linker-batch-install-detect-removal.md` (CP-1327 2단계). `portfolio.md` Case 1 회고 수정 완료 — Case 11 서사와 정합

- ✅ **Case 5 K8s**: "K8s 구축 후 EC2 전환"이 맞음 (후자). 현재 발행본엔 K8s 언급 없음 — Case 5 보강 시 판단 서사로 사용 가능
- ✅ **blue-green 보류 근거** "환경·빌드 2배 비용"은 실제 판단 맞음 (`portfolio.md:141` 그대로 유지)
- ✅ **CAM 12종** 확정 (현재 문서 12종 통일 유지)

> 빌드: 루트 `resume.md`·`portfolio.md` 수정 시 `.github/workflows/deploy-pages.yml`이 Pages 자동배포. 커밋 전 `git config --local user.email`이 `imagoworks.ai` 아닌지 확인 (CLAUDE.md §8).

---

## 진행 규칙 요약

1. **사실 발굴은** [findings/README.md](./findings/README.md) 4시그널 + 출처 태그. **귀속은** git/Confluence author 교차검증.
2. **작성은** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 최종 담백 스타일. **평가는** [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md).
3. 새 사실/수치를 박을 때마다 "사용자가 답했나, 내가 만들었나" 자체 검증. 출처 없으면 못 박음.
4. 커밋 직전 `git config --local user.email`이 `imagoworks.ai`가 아닌지 확인.

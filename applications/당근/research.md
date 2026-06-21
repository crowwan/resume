# 당근(Karrot) — JD·엔지니어링 컬처 리서치

> 작성: 2026-06-21 / 목적: 당근 Frontend(커머스) 지원 이력서 전략 근거 + 면접 준비
> 공고 직접 fetch 성공(2회 교차검증). 기술블로그·FEConf·OSS 레포 교차.

## 1. JD 핵심 요구

**타깃 직무: Software Engineer, Frontend - 커머스 (커머스팀, 프로덕트 FE)**
- 도메인: 중고거래 본진이 아니라 **이커머스**(상품 탐색→구매, 이벤트/기획전, 판매자 관리도구·대시보드).

**주요 업무**
- 매력적인 커머스 UX — 상품 탐색~구매 인터페이스, **웹뷰·웹 일관 경험**, 이벤트/기획전 빠른 배포
- **빠른 실험·학습** — 실험적 기능 빠르게 구현·테스트, **A/B 테스트 일상화**, 데이터 기반 개선
- 판매자 관리 도구 — 상품 등록·주문/재고·정산 페이지, **판매 성과 데이터 시각화 대시보드**
- ★ **AI로 개발 속도 극대화** — Claude Code·Cursor로 UI 컴포넌트·프로토타입 빠르게, 반복 작업 AI 자동화 → 확보한 시간을 UX 혁신에 투자

**자격 요건**
- FE 서비스/프로젝트를 **주도적으로 개발·운영**한 경험
- 고객 문제 해결 집착 + **오너십**으로 끝까지 완수
- 새 기술 스택 빠른 적응, **빠른 실행과 확장성 균형**
- 직군 무관 열린 협업, 실패로부터 배우는 자세
- **비즈니스 의사결정 전이라도 엔지니어링 관점에서 자율 판단·실행** (← 자율성 강한 시그널)
- ★ **AI 도구(Claude Code·Cursor) 적극 활용**해 생산성↑·비즈니스 임팩트 집중

**우대 사항**
- TypeScript·React 개발 경험
- **이커머스 도메인** 이해·경험
- **복잡한 상태 관리(Recoil·Zustand)** 경험
- **GraphQL 아키텍처 + Relay** 개발 경험
- **실행 환경(웹브라우저·인앱 웹뷰 등)과 환경 정책 이해**

**명시 기술스택**: TypeScript · React · Recoil · Zustand · GraphQL · Relay · Claude Code · Cursor

## 2. 회사가 푸는 문제 · 일하는 방식

- **웹뷰 배포 진화**: 파일 기반 웹뷰(Airport)의 한계(웹 표준 비활성·캐시 자체관리·권한 분산)를 겪고 **remote webview(웹 서버로 회귀)**로 재설계. "은총알 대신 상황에 맞는 트레이드오프, 익숙함에 안주하지 않고 진화"라는 명시 철학. (배포 인프라는 S3/Vercel/Netlify/Cloudflare를 **팀 자율 선택**)
- **웹뷰 스택 네비게이션**: 앱 안 웹뷰에서 네이티브 같은 전환/스와이프백/스크롤 보존을 위해 **stackflow** 자체 개발→OSS(star 1,000+). 마이크로커널+플러그인 생태계.
- **크로스플랫폼 디자인시스템**: 1.5년에 걸쳐 **seed-design** 구축(CSS Variables, Scale→Semantic 2계층 토큰).
- **일하는 방식**:
  - **목적조직**(PM·PD·BE 2·FE 2 = 6인) + **챕터**(같은 스택 횡적 교류로 사일로 보완)
  - **코드리뷰 Pn 룰**(P1 꼭 반영 ~ P5 반영 안 해도 됨)으로 피드백 강도 명시
  - **자율성**: 배포 인프라조차 팀이 직접 선택. 공고의 "자율 판단·실행"과 일치
  - **글쓰기·오픈소스 문화**: 기술블로그 활발, stackflow·seed-design OSS 공개

## 3. FE 기술 스택

- TypeScript + React, **GraphQL + Relay**, 상태관리 **Recoil·Zustand**
- 실행환경 **인앱 웹뷰 + 웹**, A/B 테스트 일상화
- 자체 OSS: **stackflow**(웹뷰 네비), **seed-design**(디자인시스템), **remote webview** 배포 아키텍처
- 모노레포는 전용 툴(Nx/Turbo)보다 **Yarn Berry workspace** 선호 경향
- 마이크로프론트엔드(Module Federation 등) 명시 채택은 **미확인**

> 당근 FE 조직 = 여러 목적조직(커머스/채팅/검색/알바/페이) + **Front-end Core(Platform) 팀**(stackflow·seed-design 등 공통 프레임워크 담당) + 챕터. **이 공고는 커머스팀(프로덕트), Platform은 별도 공고.** → 지원자의 플랫폼·공통기반 강점은 Platform 팀과 더 맞닿음(별도 검토 가치).

## 4. 지원자 매칭

1. ★ **AI를 개발에 결합** (공고 핵심 + 희소 차별) — EC2 Claude 상시 구동 변경 감지, TC-Verify 2-에이전트 검증, "AI로 빠르게 바뀌는 코드를 검증·가드로 안전하게". 공고가 AI 활용을 자격·업무에 명시 → **최강 접점**. 단 본인은 "검증형 활용"이 강점(메모리 `resume-ai-usage`) — 당근의 "속도 가속"과 결합하면 "빠르게 만들되 안전하게"로.
2. ★ **자율 주도 운영** (자격 직격) — Linker 단독 설계·구현·운영, 격리 재현 환경 주도 구축. 공고 "주도적 개발·운영, 오너십, 자율 판단·실행"과 일치.
3. ★ **실행환경 정책 이해** (우대 직격) — Linker에서 **Chrome LNA 정책**에 막힌 로컬 통신을 Custom Protocol로 재설계, 설치 감지 휴리스틱 한계 수용. 공고 우대 "실행환경(웹브라우저·인앱 웹뷰)과 정책 이해"와 결이 같음(단 본인은 Electron/데스크톱, 인앱 웹뷰 실무는 아님 → 정직하게 인접 경험으로).
4. **복잡한 상태관리** (우대) — Recoil 실무 경험(기술스택), 썸네일 공유 캐시·실패 캐시, 구독·결제 서버/클라 상태 분리.
5. **이커머스 인접** — B2B 구독·결제(Stripe·결제 워크플로 전체)는 C2C 커머스와 도메인은 다르나 결제·권한 경험 인접.

**약한 영역(정직)**: GraphQL/Relay 미경험, C2C 커머스 도메인 미경험, A/B 실험 문화 미경험, 인앱 웹뷰 실무(Electron은 있으나 웹뷰는 아님).

## 5. 이력서 강조 전략

- 오늘의집 Content와 달리 **본인 강점이 직무 요구와 정면으로 맞는 공고**. 무리한 리프레이밍 불필요.
- 첫 축 **"AI를 판단 단계에 결합 + 검증으로 안전하게"** — 당근이 명시적으로 원하는 AI 활용을, 단순 코드 자동완성이 아니라 변경 감지·테스트 선별·검증 가드로 한 단계 위에서 보여줌. **FE 지원자 중 희소한 차별점.**
- 둘째 축 **자율 주도 운영**(Linker·격리환경) + **실행환경 정책 이해**(LNA·Custom Protocol). 공고 자격·우대 직격.
- 셋째 축 상태관리·복잡한 화면(썸네일·구독결제).
- 약한 영역(GraphQL/Relay·C2C 커머스·A/B·웹뷰)은 **수치 지어내지 말고 성장 서사로 정직하게**. 당근 컬처(실패로부터 배움·자율)상 정직이 유리.
- ★ **Platform 팀 별도 공고도 확인 가치** — 지원자의 플랫폼·공통기반·검증 인프라 강점(테스트 격리·모노레포·MFE·관측)은 stackflow·seed-design을 만드는 Front-end Core(Platform) 팀과 더 정렬됨. 커머스 vs Platform 중 어디에 낼지 본인 선호로 결정.

## 출처
- 공고(직접 fetch ×2): https://careers.daangn.com/jobs/role/6692173003/
- 채용 목록: https://careers.daangn.com/jobs/
- FEConf 2023(stackflow·seed): https://medium.com/daangn/당근-feconf-2023-3994d7813f15
- 웹 배포 #1(파일 기반 웹뷰): https://medium.com/daangn/당근마켓에-웹-프로젝트-배포하기-1-파일-기반-웹뷰-d312b17e697c
- 웹 배포 #2(remote webview): https://medium.com/daangn/당근마켓에-웹-프로젝트-배포하기-2-웹-서버로-돌아가기-3030daea456c
- stackflow: https://github.com/daangn/stackflow
- seed-design: https://seed-design.io/ · https://github.com/daangn/seed-design
- 당근 GitHub: https://github.com/daangn
- 조직·컬처(개발자 10문10답 등): https://about.daangn.com/blog/

## 미확인 (정직)
- 마이크로프론트엔드(Module Federation 등) 채택 여부 — 미확인
- Vite/번들러 전사 표준 여부 — FEConf 부스 설문 언급뿐, 단정 불가
- 커머스팀 내부 Next.js/SSR 실제 사용 — 공고엔 "팀별 SSR 자유"만, 구체 미확인

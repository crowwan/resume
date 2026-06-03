# 오늘의집(버킷플레이스) — JD·엔지니어링 컬처 리서치

> 작성: 2026-06-03 / 목적: 오늘의집 FE 지원 이력서 전략 근거 + 면접 준비
> ⚠️ 타깃 JD(173769 = Frontend Engineer, Content)는 403/JS 렌더링으로 직접 fetch 실패 → 검색 인덱스로 본문 복원. 우대는 동사 FE 계열 공고(rallit 미러)로 보강.

## 1. JD 핵심 요구

**타깃 직무: Frontend Engineer, Content (콘텐츠개발팀)**
- 도메인: 사용자가 콘텐츠/커뮤니티를 처음 만나는 웹 경험. 인테리어·라이프스타일 콘텐츠.
- 필수: **웹 FE 2년 이상**, React·TypeScript 등 모던 프레임워크 이해 (← 지원자 2년 9개월 부합)
- 책임: 콘텐츠/커뮤니티 웹 인터페이스·데이터 흐름 설계·구현, 디자이너·기획·백엔드 협업, **성능·접근성·유지보수성** 고려 React·TS 개발, **LLM 기반 에이전트로 사용자 인터랙션 설계**

**FE 계열 공통 자격/우대 (rallit 미러)**
- 자격: React, **SSR 처리 경험**, **MSA·BFF(Backend For Frontend) 이해**, SPA 이해
- 우대: 소규모 프로젝트 리딩, **데이터 기반 성능 최적화**, **테스트·CI/CD**, 애자일, **컴포넌트/라이브러리 제작**, BFF 설계, 인테리어 관심

> 오늘의집 FE는 도메인별 다수 팀: Content / **Client Foundation**(플랫폼·공통기반) / Commerce / User / GenAI·3D. 지원자의 "플랫폼 아키텍처" 강점은 Client Foundation 성격과도 맞닿음.

## 2. 회사가 푸는 문제 · 일하는 방식

- 도메인: 가구·인테리어 O2O 커머스 + 콘텐츠/커뮤니티. 파트너사 정산(일정산) 등 운영 효율화.
- **데이터 기반 의사결정**: "고객이 정말 이걸 원하는 게 맞아?"를 끊임없이 묻고 가설→검증.
- **집요함·탁월함 + 속도**: "대충 넘어가는 법이 없다", 품질과 속도 동시.
- 최근 기술 과제:
  - **MSA 전환**: Rails 모놀리식(.erb/jBuilder, jQuery) → React 점진 전환. Git Subtree 클라이언트 분리, 프록시 라우팅, Node SSR 스레드풀, API Gateway. FE/BE 결합(공통 도메인·인증 쿠키) 푸는 게 난제.
  - **AI/LLM 내재화**: AI 인턴 '오집사' 등. 타깃 JD에 "LLM 에이전트로 인터랙션 설계" 명시.

## 3. FE 기술 스택
- React + TypeScript, SSR(Node), **MSA·BFF**, API Gateway 엣지 라우팅
- 관심 가치 축: **성능 최적화(데이터 기반), 접근성, 유지보수성, 컴포넌트/라이브러리화, 테스트·CI/CD, LLM 에이전트 UX**
- 대규모 멀티팀 프론트엔드 운영

## 4. 지원자 매칭

1. **품질 자동화·테스트·CI/CD** (우대 직격) — Playwright E2E + Docker 격리 재현 + GitHub Actions self-hosted 20→6분. "집요함" 컬처핏. **최강 접점.**
2. **데이터 기반 성능 최적화** (우대+컬처) — 단, 지원자 강점이 웹 CWV보다 빌드/인프라 성능 → 프레이밍 주의.
3. **플랫폼 아키텍처/모듈화** — NX 모노레포·MFE·런타임분리 ↔ 오늘의집이 실제 겪은 **MSA FE 분리** 서사와 문제 결이 동일.
4. **LLM/AI 활용** (타깃 JD 고유) — EC2 Claude AI 변경 감지. FE 지원자 중 희소.
5. **B2B 구독/결제 + 글로벌** — Commerce/User팀 인접.

## 5. 이력서 강조 전략

첫 카드 **"품질을 자동화로 끝까지 책임진 FE"** — E2E+Docker 격리+AI 변경감지를 "테스트 인프라 0→1 서사"로 묶어 맨 앞(우대 테스트·CI/CD + 컬처 집요함 동시 타격). 2축 **플랫폼 아키텍처(모노레포+MFE)**로 회사의 MSA FE 분리 문제와 결 동일함을 보여줌. **약한 영역(웹 런타임 성능 CWV·SSR·BFF·대규모 트래픽)은 수치 지어내지 말고 성장 서사로 정직하게** — 이 회사 컬처상 오히려 유리. SSR/BFF는 JD 필수에 가까우므로 Electron·Node 빌드 경험을 인접 근거로 연결하되 과장 금지.

## 출처
- 타깃 JD: https://bucketplace.career.greetinghr.com/ko/o/173769 (검색 복원)
- FE 공통 자격·우대: https://www.rallit.com/positions/1061/
- MSA FE 분리: https://www.bucketplace.com/post/2021-12-03-오늘의집-msa-phase-1-프론트엔드-분리작업/
- API Gateway: https://www.bucketplace.com/post/2021-12-30-msa-phase-1-api-gateway/
- 조직/컬처: https://www.bucketplace.com/post/2025-02-19-오늘의집은-여전히-시작점에-서있습니다/
- AI 내재화: https://www.bucketplace.com/post/2025-01-17-오늘의집-ai-인턴-오집사-개발-여정/

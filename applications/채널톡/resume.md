# 김진완 | Frontend Engineer

프론트엔드 아키텍처·상태관리 · Electron 데스크톱 · 품질 자동화

- [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023년 9월부터 AI 기반 치과 CAD/CAM SaaS인 Dentbird에서 프론트엔드 개발자로 일하며, 화면 구현뿐 아니라 모노레포·환경 분리, Electron 데스크톱 앱, 테스트·빌드·배포 자동화까지 제품이 돌아가는 구조를 맡아 왔습니다. 여러 레포에 흩어진 클라이언트 앱과 공용 라이브러리를 하나의 모노레포로 모았고, 모노레포 도구는 새로 고르는 대신 기존 NX의 전환 이득이 이관·재학습 비용을 넘지 못한다고 보아 유지하는 판단을 했습니다. 채널톡이 Nx 대신 yarn workspace와 TS Project References를 택한 글에서, 도구보다 그 판단의 결이 닿았습니다. 상담사용 데스크톱 앱과 멀티 환경 일관 동작을 다뤄온 경험도 데스크 개발과 맞닿는다고 보아 지원합니다.

---

## 경력

**이마고웍스 (ImagoWorks)** | 프론트엔드 개발자 | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS인 Dentbird의 프론트엔드를 맡아, 웹·데스크톱 앱 구현부터 모노레포 통합·런타임 환경 분리·품질 자동화 같은 플랫폼 기술 결정까지 담당해 왔습니다.

---

## 주요 프로젝트

### 프론트엔드 아키텍처·디자인 패턴

계정·구독 클라이언트에 FSD·컴포넌트 패턴·선언적 에러 처리를 도입하고, 그 한계까지 겪으며 "패턴 도입의 전제"를 배웠습니다.

- FSD로 도메인 분리, Compound Component·Render Props로 관심사 분리 (단점·적용 한계까지 문서화)
- ErrorBoundary 기반 선언적 에러 처리 + 화면 단위 Fault Tolerance 설계, BusinessLogicError 커스텀 분기
- 3개 앱에 중복되던 CaseInfo 다이얼로그를 Compound + FormProvider 공통 컴포넌트로 추출 (약 66% 감소)

`기술` React · TypeScript · FSD · Compound/Render Props · ErrorBoundary

### 복잡한 화면 — 렌더링 성능·상태관리

사용자가 가장 자주 보는 케이스 목록·뷰어 화면의 성능과 상태를 다뤘습니다.

- 썸네일을 IntersectionObserver 배치 lazy-loading + TTL 캐시 + 무한 재요청 방지로 처리
- 목록↔3D 리뷰 토글의 URL 상태 동기화 race를 비교 경계 축소로 근본 해결

`기술` React · TanStack Query · IntersectionObserver · Three.js

### 모노레포 통합·환경 분리

여러 클라이언트 앱과 공용 라이브러리를 단일 NX 모노레포로 통합하고, 환경·도메인 구성을 일원화했습니다.

- 별도 레포 클라이언트 앱 2종·공용 라이브러리 6종을 Git Subtree로 이관 (네임스페이스 충돌 리네임)
- 환경별 도메인·URL 구성을 단일 통합 라이브러리로 일원화 (팀의 런타임 환경 분리·격리 재현이 이를 기반으로 동작)
- 중복 업로드 89줄·14개 스크립트로 흩어진 다국어 관리를 단일 스크립트로 중앙화
- NX 자동 타겟 추론이 일으키던 빌드 실패(OOM)를 진단, 불필요한 산출물 설정을 제거해 해소

`기술` TypeScript · NX · Git Subtree · i18n

### 웹·로컬 CAM 연동 Electron 데스크톱 앱

웹 보철 케이스를 사용자 PC의 CAM 소프트웨어로 전달하는 사내 연동 앱을 단독 설계·운영했습니다.

- Chrome LNA 정책으로 막힌 로컬 서버 통신을 Custom Protocol 중간 레이어로 재설계
- 좌표계·파일 전달 방식이 제각각인 외부 CAM 12종을 단일 변환·연동 인터페이스로 통합
- 명세 없던 기존 CAM 연동을 특성화 테스트로 고정한 뒤 재구현

`기술` Electron · React · TypeScript · Custom Protocol · Datadog

### 데스크톱 앱 빌드·배포 인프라

담당자 로컬 PC에서 돌던 두 데스크톱 앱의 빌드·서명·배포 파이프라인을 재설계했습니다.

- macOS 빌드 파이프라인 재설계로 전체 33~39분에서 17~24분으로 단축 (약 -56%, 아티팩트 757MB→334MB)
- Windows 코드 서명 에이전트를 물리 빌드머신에 직접 설치·풀 등록해 단독 운영 (서명 USB·인프라팀 의존 제거)

`기술` Electron · electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3

### 테스트·품질 자동화 인프라

테스트가 거의 없던 제품에 격리 재현 기반의 자동 회귀 감지 체계를 구축했습니다.

- 커밋 시점 클라이언트·서버·DB를 하나의 컨테이너로 통합해 결정론적 격리 재현 환경 구성
- 백오피스 전 영역에 E2E를 구축하고(로그인 중복을 공통 헬퍼로 약 93% 감소), 흩어진 Playwright E2E를 모노레포로 통합·Page Object 리팩토링으로 중복 제거
- 커밋 변경을 분석해 연관 QA 테스트 케이스만 자동 선별·실행하고 결과를 Teams로 보고하는 AI 변경 감지 구축
- 실행 인프라를 EC2로 구성하고 10분 간격 크론잡으로 자동 실행

`기술` Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted)

### B2B 구독·결제 시스템 프론트엔드

일회성 크레딧에서 반복 구독으로 전환하는 시점에, 글로벌 멀티테넌트 결제·권한 도메인 프론트엔드를 전담했습니다.

- 플랜 업그레이드·시트 구매·구독 취소·재개·쿠폰·결제수단·결제 히스토리까지 구독 워크플로우 전체를 구현
- 결제 상태를 서버·Stripe 동기화(SoT) 기준으로 두고 무한 반복 방지 폴링·origin 격리된 결제 팝업으로 외부 연동 복원력 확보
- 부분 실패에도 핵심 흐름(내역 조회·구독 취소·뒤로가기)이 살아남도록 의존 관계 기준 Fault Tolerance를 제안 (착수 전)

`기술` React · TypeScript · TanStack Query · Stripe · ErrorBoundary · Playwright(E2E)

### 공통 모듈 통합 — Micro Frontend

도메인·관리팀이 나뉜 여러 서비스가 공유하는 공통 기능을, 한 곳에서 고치면 모두 반영되도록 런타임 모듈로 통합했습니다.

- 공통 기능 4종(설정·내보내기·탐색기·뷰어)을 iframe + postMessage 런타임 모듈로 통합, cross-origin 격리 헤더·호스트 화이트리스트로 임베드 경계 잠금
- Module Federation은 초기·소비처 설정 부담을 확인하고 별도 콘솔 서비스에만 적용

`기술` NX · iframe + postMessage · Module Federation

### 기업 랜딩 페이지 풀스택

입사 첫 업무로 기업 랜딩을 단독 리뉴얼하고, 이후 2년 넘게 단일 라인으로 운영했습니다.

- 번역 키 오타가 런타임에야 드러나던 문제를, 영어 단일 소스에서 타입을 자동 생성해 없는 키를 컴파일 타임에 차단하는 타입세이프 i18n으로 전환
- 화면부터 서버 API까지 풀스택으로 개발 (Next.js · Fastify · MongoDB)

`기술` Next.js · 타입세이프 i18n · Fastify · MongoDB

### 프론트엔드 에러 관측·복원력 표준화

에러는 잡히지만 어디서 왜 났는지 추적되지 않던 상태를, 5개 앱에 공통 표준으로 정리했습니다.

- 3-layer ErrorBoundary 구조(Root → Section → Feature)로 에러를 계층별로 격리, 5개 앱에 적용
- 에러를 Datadog 관측 표준에 맞춰 직렬화, 한 메시지로 뭉쳐 보이던 실패 124건을 원인별로 추적 가능하게
- 최상위 안전망에 외부 의존을 두지 않아, 안전망이 자기 의존성 때문에 죽지 않도록 설계

`기술` React · TypeScript · ErrorBoundary · Datadog RUM

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron |
| **State / UI** | TanStack Query, Recoil · MUI, Emotion |
| **Build / Arch** | NX, pnpm, Git Subtree, Module Federation, iframe + postMessage, 런타임 Config |
| **3D / Graphics** | Three.js, WebGL, draco3d, SRGB ColorManagement |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택, 조직 관리 백오피스 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

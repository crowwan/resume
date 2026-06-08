# 김진완 | 프론트엔드 개발자

Electron 데스크톱 · 프론트엔드 플랫폼 아키텍처 · 품질 자동화 중심

- [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023년 9월부터 AI 기반 치과 CAD/CAM SaaS인 Dentbird에서 프론트엔드 개발자로 일하고 있습니다. 화면 구현뿐 아니라 Electron 데스크톱 앱, 모노레포·환경 분리, 테스트·빌드·배포 자동화까지 제품이 돌아가는 구조를 맡아 왔습니다.

---

## 핵심 역량

- **프론트엔드 플랫폼**: NX 모노레포 통합, 환경·도메인 구성 일원화, 공통 모듈(MFE) 분리
- **데스크톱**: Electron 앱 설계·운영, 빌드·코드 서명·자동 업데이트 인프라
- **품질 자동화**: Playwright E2E, 커밋 단위 컨테이너 격리 재현, 시각 회귀 CI, 에러 관측 표준화
- **제품 도메인**: B2B 구독·결제 프론트엔드, 글로벌 멀티테넌트·다국어, 외부 CAM 연동

---

## 경력

**이마고웍스 (ImagoWorks)** | 프론트엔드 개발자 | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS인 Dentbird의 프론트엔드를 맡아, 웹·데스크톱 앱 구현부터 모노레포 통합·런타임 환경 분리·품질 자동화 같은 플랫폼 기술 결정까지 담당해 왔습니다.

---

## 주요 프로젝트

> 각 프로젝트의 배경·대안 비교·회고는 [포트폴리오](portfolio.html)에 정리했습니다.

### 웹·로컬 CAM 연동 Electron 데스크톱 앱

웹 보철 케이스를 사용자 PC의 CAM 소프트웨어로 전달하는 사내 연동 앱을 단독 설계·운영했습니다.

- Chrome LNA 정책으로 막힌 로컬 서버 통신을 Custom Protocol 중간 레이어로 재설계
- 좌표계·파일 전달 방식이 제각각인 외부 CAM 12종을 단일 변환·연동 인터페이스로 통합
- 명세 없던 기존 CAM 연동을 특성화 테스트로 고정한 뒤 재구현

`기술` Electron · React · TypeScript · Custom Protocol · Datadog

### 모노레포 통합·환경 분리

여러 클라이언트 앱과 공용 라이브러리를 단일 NX 모노레포로 통합하고, 환경·도메인 구성을 일원화했습니다.

- 환경별 도메인·URL 구성을 단일 통합 라이브러리로 일원화 (팀의 런타임 환경 분리·격리 재현이 이를 기반으로 동작)
- 별도 레포 클라이언트 앱 2종·공용 라이브러리 6종을 Git Subtree로 이관
- NX 자동 타겟 추론이 일으키던 빌드 실패(OOM)를 진단, 불필요한 산출물 설정을 제거해 해소

`기술` TypeScript · NX · Git Subtree · i18n

### 테스트·품질 자동화 인프라

테스트가 거의 없던 제품에 격리 재현 기반의 자동 회귀 감지 체계를 구축했습니다.

- 커밋 시점 클라이언트·서버·DB를 컨테이너로 묶어 결정론적 격리 재현 환경 구성
- 흩어진 Playwright E2E를 모노레포로 통합, Page Object·세션 재사용으로 중복 제거
- 커밋 변경을 분석해 연관 QA 테스트 케이스만 자동 선별·실행하고 결과를 Teams로 보고하는 AI 변경 감지 구축
- 실행 인프라를 EC2로 구성하고 10분 간격 크론잡으로 자동 실행

`기술` Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted)

### 3D 렌더링 품질 자동화

팀이 AI로 빠르게 진행한 사내 3D 라이브러리→Three.js 마이그레이션에서, 두 엔진의 렌더 이미지를 눈으로 맞추느라 미세조정 값에 기댄 색·조명을 원본 엔진 동작 기준으로 다시 정리했습니다.

- 이미지 맞춤으로 들어간 미세조정 값을 원본 엔진 실제 동작 근거로 Three.js 표준 기능으로 대체 (약 1,420줄 정리)
- 격리 컨테이너에서 baseline을 고정 생성해 환경 차를 제거하고, Playwright 스크린샷 비교로 3D 렌더 회귀를 가드

`기술` Three.js · draco3d · WebGL · SRGB ColorManagement · Playwright

### 데스크톱 앱 빌드·배포 인프라

담당자 로컬 PC에서 돌던 두 데스크톱 앱의 빌드·서명·배포 파이프라인을 재설계했습니다.

- macOS 빌드 파이프라인 재설계로 약 36분에서 20분으로 단축
- Windows 코드 서명 에이전트를 물리 빌드머신에 직접 설치·풀 등록해 단독 운영 (서명 USB·인프라팀 의존 제거)

`기술` Electron · electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3

### B2B 구독·결제 시스템 프론트엔드

일회성 크레딧에서 반복 구독으로 전환하는 시점에, 글로벌 멀티테넌트 결제·권한 도메인 프론트엔드를 전담했습니다.

- 플랜 업그레이드·시트 구매·구독 취소·재개·쿠폰·결제수단·결제 히스토리까지 구독 워크플로우 전체를 구현
- 부분 실패에도 핵심 흐름(결제 내역 조회·구독 취소·뒤로가기)이 살아남도록 의존 관계 기준으로 Fault Tolerance 설계

`기술` React · TypeScript · TanStack Query · Stripe · ErrorBoundary · Playwright(E2E)

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

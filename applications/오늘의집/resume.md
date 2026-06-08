# 김진완 | 프론트엔드 개발자

품질 자동화 · 프론트엔드 플랫폼 아키텍처 · Electron 데스크톱 중심

- [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023년 9월부터 AI 기반 치과 CAD/CAM SaaS인 Dentbird에서 프론트엔드 개발자로 일하고 있습니다. 화면 구현뿐 아니라 테스트·빌드·배포 자동화, 모노레포 통합과 공통 모듈 분리까지, 제품이 돌아가는 구조를 맡아 왔습니다. 테스트가 거의 없던 제품에 재현 가능한 환경과 자동 회귀 감지를 직접 만든 경험은 "대충 넘어가지 않는" 품질과 속도를 함께 보는 오늘의집의 일하는 방식과 맞닿아 있다고 생각합니다. 별도 레포로 흩어진 앱을 모노레포로 통합하고 공통 기능의 모듈 경계를 다시 그어 온 경험은 오늘의집이 겪은 모놀리식의 점진적 프론트엔드 분리 문제와도 결이 같습니다.

---

## 핵심 역량

- **품질 자동화·CI/CD**: Playwright E2E, 커밋 단위 컨테이너 재현, 변경 기반 테스트 자동 선별, 시각 회귀 CI, 에러 관측 표준화
- **프론트엔드 플랫폼**: NX 모노레포 통합, 환경·도메인 구성 일원화, 공통 모듈(MFE) 분리
- **데스크톱**: Electron 앱 설계·운영, 빌드·코드 서명·자동 업데이트 인프라
- **제품 도메인**: B2B 구독·결제 프론트엔드, 글로벌 멀티테넌트·다국어

---

## 경력

**이마고웍스 (ImagoWorks)** | 프론트엔드 개발자 | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS인 Dentbird의 프론트엔드를 맡아, 웹·데스크톱 앱 구현부터 모노레포 통합·런타임 환경 분리·품질 자동화 같은 플랫폼 기술 결정까지 담당해 왔습니다.

---

## 주요 프로젝트

### 테스트·품질 자동화 인프라

테스트가 거의 없던 제품에 격리 재현 기반의 자동 회귀 감지 체계를 구축했습니다.

- 커밋 시점 클라이언트·서버·DB를 컨테이너로 묶어 결정론적 격리 재현 환경 구성
- 흩어진 Playwright E2E를 모노레포로 통합, Page Object·세션 재사용으로 중복 제거
- 커밋 변경을 분석해 연관 QA 테스트 케이스만 자동 선별·실행하고 결과를 Teams로 보고하는 AI 변경 감지 구축
- 만성 실패하던 일일 검증을 원인별로 진단해 근본 수정 (결제 테스트 누락 약 28건 → 0)
- 실행 인프라를 EC2로 구성하고 10분 간격 크론잡으로 자동 실행

`기술` Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted)

### 3D 렌더링 품질 자동화

팀이 AI로 빠르게 진행한 사내 3D 라이브러리→Three.js 마이그레이션에서, 두 엔진의 렌더 이미지를 눈으로 맞추느라 미세조정 값에 기댄 색·조명을 원본 엔진 동작 기준으로 다시 정리했습니다.

- 이미지 맞춤으로 들어간 미세조정 값을 원본 엔진 실제 동작 근거로 Three.js 표준 기능으로 대체 (약 1,420줄 정리)
- 격리 컨테이너에서 baseline을 고정 생성해 환경 차를 제거하고, Playwright 스크린샷 비교로 3D 렌더 회귀를 가드 (3개 앱 공유 baseline)

`기술` Three.js · draco3d · WebGL · SRGB ColorManagement · Playwright

### 모노레포 통합·환경 분리

여러 클라이언트 앱과 공용 라이브러리를 단일 NX 모노레포로 통합하고, 환경·도메인 구성을 일원화했습니다.

- 환경별 도메인·URL 구성을 단일 통합 라이브러리로 일원화 (팀의 런타임 환경 분리·격리 재현이 이를 기반으로 동작)
- 별도 레포 클라이언트 앱 2종·공용 라이브러리 6종을 Git Subtree로 이관
- NX 자동 타겟 추론이 일으키던 빌드 실패(OOM)를 진단, 불필요한 산출물 설정을 제거해 해소

`기술` TypeScript · NX · Git Subtree · i18n

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

### B2B 구독·결제 시스템 프론트엔드

일회성 크레딧에서 반복 구독으로 전환하는 시점에, 글로벌 멀티테넌트 결제·권한 도메인 프론트엔드를 전담했습니다.

- 플랜 업그레이드·시트 구매·구독 취소·재개·쿠폰·결제수단·결제 히스토리까지 구독 워크플로우 전체를 구현
- 부분 실패에도 핵심 흐름(결제 내역 조회·구독 취소·뒤로가기)이 살아남도록 의존 관계 기준으로 Fault Tolerance 설계

`기술` React · TypeScript · TanStack Query · Stripe · ErrorBoundary · Playwright(E2E)

### 웹·로컬 CAM 연동 Electron 데스크톱 앱

웹 보철 케이스를 사용자 PC의 CAM 소프트웨어로 전달하는 사내 연동 앱을 단독 설계·운영했습니다.

- Chrome LNA 정책으로 막힌 로컬 서버 통신을 Custom Protocol 중간 레이어로 재설계
- 좌표계·파일 전달 방식이 제각각인 외부 CAM 12종을 단일 변환·연동 인터페이스로 통합
- 명세 없던 기존 CAM 연동을 특성화 테스트로 고정한 뒤 재구현

`기술` Electron · React · TypeScript · Custom Protocol · Datadog

---

## 보완하고 싶은 영역

대규모 웹 트래픽 환경의 Core Web Vitals 최적화, SSR, BFF, 접근성(a11y)은 아직 실무 경험이 깊지 않습니다. Next.js 기반 풀스택(기업 랜딩 + Fastify 서버 API)으로 서버사이드 JS 런타임을 다뤄봤고 제품에서 런타임 환경 분리·번들 구조·빌드 성능은 직접 맡아 왔지만, 사용자 규모가 큰 웹 런타임 성능과 접근성은 입사 후 우선 채우고 싶은 영역입니다.

LLM 기반 사용자 인터랙션 설계는 직접 해본 적이 없습니다. 다만 변경 감지·테스트 선별처럼 AI를 개발 파이프라인의 판단 단계에 결합해 본 경험이 있어, 그 감각을 사용자 대면 인터랙션으로 확장하는 데 관심이 큽니다.

---

## 기술 스택

| 분류              | 기술                                                                          |
| ----------------- | ----------------------------------------------------------------------------- |
| **Language**      | TypeScript, JavaScript                                                        |
| **Frontend**      | React 18/19, Next.js                                                          |
| **State / UI**    | TanStack Query, Recoil · MUI, Emotion                                         |
| **Build / Arch**  | NX, pnpm, Git Subtree, Module Federation, iframe + postMessage, 런타임 Config |
| **3D / Graphics** | Three.js, WebGL, draco3d, SRGB ColorManagement                                |
| **Testing**       | Playwright, Jest, Vitest, MSW                                                 |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3)             |
| **Desktop**       | Electron                                                                     |
| **Monitoring**    | Datadog RUM/Logs                                                              |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택, 조직 관리 백오피스 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

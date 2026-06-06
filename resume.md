# 김진완 | 프론트엔드 개발자

Electron 데스크톱 · 프론트엔드 플랫폼 아키텍처 · 품질 자동화 중심

- [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023년 9월부터 AI 기반 치과 CAD/CAM SaaS인 Dentbird에서 프론트엔드 개발자로 일하고 있습니다. 화면 구현뿐 아니라 Electron 데스크톱 앱, 모노레포·환경 분리 같은 플랫폼 토대, 테스트·빌드·배포 자동화까지 제품이 돌아가는 구조를 맡아 왔습니다.

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

### 웹·로컬 소프트웨어 연동 Electron 데스크톱 앱

웹에서 만든 보철 케이스를 사용자 PC의 CAM 소프트웨어로 전달하는 사내 연동 앱(Linker)을 단독 운영했습니다. 다운로드·압축 해제·수동 투입을 거치던 과정을 자동 전달 흐름으로 재설계했습니다.

- 초기 로컬 서버 방식이 Chrome LNA 정책에 막혀, 통신 방식을 다시 검토한 끝에 Custom Protocol 중간 레이어로 재설계
- WebSocket은 곧 같은 LNA 정책 적용 대상이라 제외하고 HTTPS 로컬 서버도 같은 이유로 무효라, 장기 안정성을 채택 기준으로 선택
- 좌표계와 파일 전달 방식이 제각각인 외부 CAM 12종을 단일 변환·연동 인터페이스로 통합
- 초기 핵심이던 로컬 서버를 수명이 다하자 직접 폐기하고, 딥링크·세션·CAM 연동 이슈를 2026년까지 담당

`기술` Electron · React · TypeScript · Custom Protocol · Datadog

### 프론트엔드 플랫폼 토대 — 모노레포 통합·환경 분리

여러 클라이언트 앱이 환경별 URL·도메인을 제각기 구성하던 구조에, 도메인 파생을 한 곳으로 모으는 토대를 만들었습니다.

- 환경별로 흩어진 도메인·URL 구성을 단일 통합 라이브러리로 일원화, 팀의 런타임 환경 분리와 격리 재현 환경이 이 위에 얹히는 시작점
- 별도 레포의 클라이언트 앱 2종·공용 라이브러리 6종을 메인 NX 모노레포로 Git Subtree 이관, 커밋 이력 보존·이후 정기 동기화까지 운영
- NX 자동 타겟 추론이 일으키던 빌드 오류를 진단, 불필요한 산출물 설정을 제거해 빌드 태스크를 11개에서 9개로 줄이고 메모리 부족 해소
- 모노레포 도구는 새로 고르지 않고 기존 NX 유지, 전환 이득이 이관·재학습 비용을 넘지 못한다는 판단

`기술` TypeScript · NX · Git Subtree · i18n

### 테스트·품질 자동화 인프라

테스트가 거의 없던 제품에, 다른 변경의 간섭 없이 재현 가능한 환경 위에서 회귀를 자동으로 잡는 체계를 만들었습니다.

- 커밋 시점의 클라이언트·서버·DB를 컨테이너로 묶어, 다른 변경의 간섭 없이 그 시점을 결정론적으로 재현
- 흩어진 Playwright E2E를 모노레포로 통합하고, Page Object와 세션 재사용으로 중복 제거
- 커밋 변경을 분석해 연관 QA 테스트 케이스만 자동 선별·실행하고 결과를 Teams로 보고하며 실패를 실제 회귀와 테스트 코드 문제로 1차 분류하는 AI 변경 감지 구축
- 실행 인프라를 K8s로 구성하려다 복잡도가 높고 빠른 적용이 어렵다고 보고, 더 단순하고 즉시 효과를 볼 수 있는 EC2로 선택

`기술` Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted)

### 3D 렌더링 품질 자동화

사내 3D 라이브러리(iwtk)→Three.js 마이그레이션은 팀이 AI 기반으로 빠르게 진행했습니다. 기존 동작을 두 엔진의 렌더 결과 이미지로 맞추다 보니 색·조명이 미세조정 값에 기대 구현된 부분이 남았고, 이를 소스 엔진의 실제 동작을 근거로 다시 정리했습니다.

- 버그 수정 중 '이미지 맞춤' 구현을 식별, iwtk의 실제 동작·구현을 근거로 Three.js 표준 기능으로 1:1 대체 가능한 부분을 옮기고 미세조정 값·메인 스레드 디코더 등 약 1,420줄 제거
- 렌더 결과를 이미지로 비교하던 과정을 채널별 평균 픽셀 차이를 metric으로 정의한 도구로 체계화, 소프트웨어 렌더러로 CI·로컬 GPU 차를 흡수하고 3개 앱 공유 baseline으로 시각 회귀 CI 구축
- 썸네일 엔진(VTK)의 Three.js 통합은 번들·성능 이득을 실측한 결과 이득이 없어 보류
- 결과 이미지를 맞추는 빠른 마이그레이션은 미세조정 값에 기대 깨지기 쉬워, 소스 엔진의 동작을 이해하고 타깃 엔진의 표준 기능으로 옮기는 접근으로 정리

`기술` Three.js · draco3d · WebGL · SRGB ColorManagement · Playwright

### 데스크톱 앱 빌드·배포 인프라

두 데스크톱 앱의 빌드가 담당자 로컬 PC에서 돌던 구조라, 그 PC가 멈추면 출시가 멈추던 파이프라인을 재설계했습니다.

- macOS 빌드 파이프라인을 재설계해 전체 33~39분을 17~24분으로 56% 단축, artifact 757MB → 334MB 동반 축소 (PR 실측)
- Windows 코드 서명 에이전트를 물리 빌드머신에 직접 설치·풀 등록해 단독 운영, 서명 USB와 인프라팀 의존 제거
- 빌드 뒤 따로 서명하면 바이너리가 바뀌어 자동 업데이트가 깨지던 문제를 서명을 빌드 과정 안에 통합해 해소
- macOS 아키텍처를 Universal에서 arm64, 다시 Intel로 11일간 3연속 번복하며 용량 2배 트레이드오프를 도입·운영·회수까지 감당

`기술` Electron · electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3

### B2B 구독·결제 시스템 프론트엔드

일회성 크레딧 결제를 반복 구독 매출로 전환하면서, 글로벌 멀티테넌트 환경의 결제·권한 도메인 프론트엔드를 전담했습니다. 기능 구현뿐 아니라 부분 실패에도 무너지지 않는 검증 구조까지 설계했습니다.

- 플랜 업그레이드·시트 구매·구독 취소·재개·쿠폰·결제수단·결제 히스토리까지 구독 워크플로우 전체를 레거시 크레딧 결제와 공존시키며 구현
- HTTP 200으로 내려오는 서버의 비즈니스 에러를 커스텀 에러로 받아 코드별로 분기, 결제 정보를 못 받아도 내역 조회·구독 취소·뒤로가기는 살아남도록 의존 관계 기준으로 Fault Tolerance 설계
- 결제 상태를 서버·Stripe 동기화 기준으로 폴링해 UI에 반영하되, 무한 반복을 막는 안전장치 추가 (3초 간격·최대 20회·언마운트 정리)
- 운영자 백오피스 측 구독 회귀 E2E 가드까지 작성

`기술` React · TypeScript · TanStack Query · Stripe · ErrorBoundary · Playwright(E2E)

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
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

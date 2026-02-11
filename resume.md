# 김진완 | Frontend Developer

---

## 자기소개

React/TypeScript 기반 SPA 개발과 모노레포·테스트 자동화 경험을 바탕으로, B2B SaaS 제품의 안정성과 개발 생산성을 높여온 3년차 프론트엔드 개발자입니다.

- 분산된 프론트엔드 프로젝트를 NX 모노레포로 통합하고 Azure Pipelines·Datadog RUM을 적용하여 배포 일관성과 관측성을 확보했습니다.
- Playwright 기반 E2E 프레임워크를 설계해 핵심 사용자 흐름을 자동 검증하고, Page Object·세션 공유로 중복 제거 및 실행 효율을 높여 릴리스 리스크를 낮췄습니다 (80+ TC, 성능 39% 개선).
- Electron 데스크톱 앱을 0→1로 설계·구축하고 16개+ CAM 소프트웨어 연동, Chrome PNA 규제 선제 대응 등 제품 전반의 기술적 의사결정을 주도하고 있습니다.

제품의 처음부터 끝까지 책임지는 것을 좋아하고, 그 과정에서 팀에 도움이 되는 구조를 남기려 합니다.
사용자가 쓰는 제품의 UX뿐 아니라, 함께 일하는 동료가 편하게 작업할 수 있는 DX도 함께 고민합니다.
기술 선택 전에 POC와 문서를 먼저 작성하며, 결정의 근거를 팀과 투명하게 공유하는 것을 중요시합니다.

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Auto Update, Code Signing) |
| **State** | TanStack Query, Recoil, Zustand |
| **UI** | MUI, Emotion, styled-components, Storybook |
| **Build** | Nx, pnpm, Webpack, Module Federation |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **3D** | VTK.js |
| **Architecture** | FSD (Feature-Sliced Design), Facade Pattern |
| **CI/CD** | Azure DevOps Pipelines, AWS EC2 CronJob |
| **Monitoring** | Datadog RUM/Logs, Electron Crash Reporter |
| **i18n** | i18next, Lokalise |
| **Backend** | Node.js, Fastify, MongoDB |

---

## 경력

### 이마고웍스 (ImagoWorks) | Frontend Developer
2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS 기업(DentBird)에서 프론트엔드 시스템 전반을 설계/구축. Micro Frontend 전환, 모노레포 통합, 도메인 아키텍처 설계 등 플랫폼 전반의 기술적 의사결정을 주도.

---

## 프로젝트

### 1. DentBird Solutions - 플랫폼 아키텍처 및 품질 자동화

| 항목 | 내용 |
|------|------|
| **기간** | 2024.06 ~ 현재 |
| **역할** | 플랫폼 아키텍처 개선, E2E 테스트 자동화, 모듈 기능 고도화 주도 (307 커밋, 275 PR) |
| **기술** | React, TypeScript, Playwright, Nx Monorepo, Azure Pipelines, Datadog, AWS EC2, Kotlin/Spring, TDD |

NX 모노레포 환경에서 플랫폼 아키텍처 개선, E2E 테스트 자동화, 모듈 기능 고도화를 주도.

**주요 성과**
- 플랫폼 아키텍처 개선
  - 분산된 **5개+ 프론트엔드 프로젝트**를 NX 모노레포로 통합
  - **Azure DevOps Pipeline CI/CD** 구성
  - **Datadog RUM 모니터링** 전사 적용
- E2E 테스트 자동화
  - Playwright 기반 E2E 테스트 프레임워크 설계, **80+ TC** 구축
  - Page Object 패턴으로 로그인 중복 코드 **93% 감소**
  - 세션 공유 전략으로 테스트 실행 성능 **39% 개선**
- AI 기반 테스트 최적화
  - **AWS EC2 CronJob** 기반 일일 자동 테스트 실행 환경 구축
  - **Claude Code CLI** 기반 AI 변경 감지 시스템 개발
  - 커밋 변경사항 자동 분석 → 관련 테스트 선별 실행
- 레거시 개선 및 모듈 고도화
  - 레거시 마이그레이션으로 TS 에러 **688→0개** 해결
  - Facade 패턴 토큰 관리 **200→33줄(85%↓)**
  - 도메인 **33→10개(70%↓)** 통합 설계
  - **Export v5 API** 마이그레이션 및 crown-io 리팩토링
  - **Viewer Module** 고도화 (3D 뷰어, Case Share, 모바일 최적화)

---

### 2. Micro Frontend 아키텍처 설계 및 운영

| 항목 | 내용 |
|------|------|
| **기간** | 2024.11 ~ 2025.07 (8개월) |
| **역할** | iframe→Module Federation 전환 설계 및 MFE 모듈 운영 (196 커밋) |
| **기술** | React 18, TypeScript, Nx, Module Federation, pnpm, VTK.js, TanStack Query, MSW, MUI |

iframe 기반 MFE 4개 모듈 운영의 한계를 경험하고, Module Federation 기반 아키텍처로 전환하여 독립 배포 체계와 중앙화된 모듈 관리를 확립.

**주요 성과**
- 아키텍처 전환
  - iframe 방식의 개별 도메인/빌드 파이프라인 분산 한계 분석
  - **Module Federation** 기반 호스트-리모트 아키텍처 설계
  - **FSD** 도입 및 ESLint 커스텀 규칙으로 레이어 간 import 방향 강제
  - 독립 배포 체계와 중앙화된 모듈 관리 확립
- 외부 시스템 연동 및 마이그레이션
  - **iframe MFE 4개 모듈**(explorer, export, viewer, setting) 운영
  - 외부 스캐너 **3개 서비스**(Medit, Shining 3D, Connect) OAuth 연동
  - 서브 도메인 마이그레이션 **무중단 전환** 완료
- 신규 모듈 개발 및 자동화
  - 사내 백오피스용 **4개 원격 모듈**을 3주 내 신규 개발 (87 커밋, **58K+ LOC**)
  - 호스트-리모트 간 **타입 공유 자동화** 스크립트 구현
  - **MSW** Mock API 환경 구축

---

### 3. DentBird Account - B2B SaaS 구독/계정 관리 시스템

| 항목 | 내용 |
|------|------|
| **기간** | 2023.09 ~ 2025.07 (1년 10개월) |
| **역할** | 구독 관리 시스템 설계 및 프론트엔드 전담 구축 (160+ 커밋) |
| **기술** | React 18, TypeScript, TanStack Query, MUI, react-hook-form, i18next, MSW, Jest |

글로벌 B2B SaaS 수익화를 위한 구독 관리 시스템을 설계하고 프론트엔드 전체를 전담 구축.

**주요 성과**
- 구독 관리 시스템 설계 및 구축
  - **SaaS 구독 관리 시스템** 전체 설계/구현
  - Free/Paid 플랜, 일할 계산, 시트 구매, 결제 관리, 구독 취소/재개, 쿠폰
- 사용자 관리 시스템
  - **Seats→Members** 시스템 전환 리팩토링
  - 멤버 초대/권한 관리 (다지역 처리 포함)
- 결제 시스템 연동
  - PostMessage 기반 Payment 페이지 연동
  - Ping-back 구현
- UI/UX 및 테스트
  - **3단계 반응형 UI** 시스템 설계 (모바일/태블릿/데스크톱)
  - **Jest + MSW** 기반 테스트 인프라 구축

---

### 4. DentBird Linker Desktop - CAM 연동 데스크톱 앱

| 항목 | 내용 |
|------|------|
| **기간** | 2024.08 ~ 현재 |
| **역할** | Electron 데스크톱 앱 0→1 설계/구축 및 Chrome PNA 대응 주도 (223+ 커밋, 8개 버전 릴리즈) |
| **기술** | Electron, React, TypeScript, Vite, Express, Nx Monorepo, Azure Pipelines, Datadog, Jest, draco3d |

치과 CAM 소프트웨어 연동 Electron 데스크톱 앱을 0→1로 설계/구축하고, Chrome PNA 대응 Custom Protocol 아키텍처 전환을 주도.

**주요 성과**
- 프로젝트 설계 및 구축
  - **Electron + Vite + React** 데스크톱 앱 0→1 설계
  - 내장 Express 서버로 웹↔데스크톱 통신 구조 설계
  - **Config 클래스** 기반 동적 소프트웨어 관리 시스템 구축
  - **16개+ CAM 소프트웨어**별 좌표계 차이를 **변환 매트릭스 알고리즘**으로 해결
  - Export Session 방식 Protocol Handler 설계
- 아키텍처 전환 및 최적화
  - **Chrome PNA** 규제 선제 대응 (WebSocket/Protocol POC → dentbird-linker:// 채택)
  - 인스톨러 **854MB→78MB (91%↓)**
- 빌드 및 배포 자동화
  - **Azure Pipelines** Windows/macOS 크로스 플랫폼 빌드 자동화
  - **EV 코드 서명** 포함
- 모니터링 및 안정성
  - **Datadog** RUM/Logs 모니터링 통합
  - Datadog FeatureFlag 적용하여 신규 방식으로 안정적 전환

---

### 5. DentBird Batch Client - Electron 데스크톱 앱

| 항목 | 내용 |
|------|------|
| **기간** | 2023.12 ~ 2025.01 (13개월) |
| **역할** | 아키텍처 설계부터 v1.0.13 프로덕션 릴리즈까지 1인 전담 개발 (347 커밋) |
| **기술** | Electron, React, TypeScript, Recoil, VTK.js, Webpack, Jest, MSW, Datadog |

치과 CAD 크라운 배치 처리 Electron 앱을 아키텍처 설계부터 v1.0.13 프로덕션 릴리즈까지 전체 개발을 주도.

**주요 성과**
- 아키텍처 및 구조 설계
  - **Electron + React + TypeScript** 데스크톱 앱 초기 세팅부터 구축
  - 라우팅/SNB/페이지 공통 컴포넌트 등 프로젝트 구조 설계
  - **IPC 통신 아키텍처** 설계 (Main/Renderer 프로세스)
- 시스템 기능 구현
  - **Deep Link**(dentbird://) 커스텀 프로토콜 구현
  - **자동 업데이트** 시스템 구현
- 빌드 및 배포 파이프라인
  - **Webpack** Main 프로세스 번들링
  - Windows/Mac **코드 서명**, 환경별(dev/qa/prod) 빌드 분리
  - Artifact 버전 관리
- 테스트 인프라 도입
  - 테스트 문화 부재 환경에서 **Jest + MSW** 단위 테스트 인프라 도입
  - 주요 모듈 테스트 코드 작성
- 모니터링 및 에러 추적
  - **Datadog RUM** 연동 (환경별 설정 분리, 빌드 시 환경변수 주입)
  - **Crash Reporter** 구축, 프로덕션 에러 추적 체계 확립

---

### 6. 기업 랜딩 페이지 풀스택 개발

| 항목 | 내용 |
|------|------|
| **기간** | 2023.09 ~ 2025.10 (약 2년) |
| **역할** | Next.js 랜딩 페이지 v3 리뉴얼 + Backoffice + 서버 API 풀스택 개발 (568 커밋) |
| **기술** | Next.js, React, TypeScript, i18next, Node.js, Fastify, MongoDB |

Next.js 기반 기업 랜딩 페이지 v3 전면 리뉴얼과 관리자 Backoffice, 서버 API를 함께 개발한 풀스택 프로젝트.

**주요 성과**
- UI/UX 구현
  - **Next.js** 기반 **10+ 페이지** 신규 구현 및 AOS 애니메이션 시스템 통합
  - 데스크탑/태블릿/모바일 3단계 반응형 UI
- 다국어 시스템
  - **i18next + TypeScript** 타입 안전한 다국어 시스템 설계
  - **3개 언어**(한/영/일) 지원, 타입 자동 생성 패키지 도입
- 성능 최적화
  - **LCP 이미지 프리로드**, Next.js Image 최적화, 캐싱 전략으로 **Lighthouse 성능 점수** 개선
- 백오피스 시스템
  - **Backoffice 조직 관리** 3계층 CRUD를 1주 53커밋으로 구현
  - **Fastify/MongoDB** 기반 일괄 수정 API 설계/개발

---

### 7. Imago Cloud Design System

| 항목 | 내용 |
|------|------|
| **기간** | 2025.04 ~ 2025.08 (4개월) |
| **역할** | MUI 기반 공용 디자인 시스템 개발 및 릴리즈 관리 (9 커밋) |
| **기술** | React 18/19, TypeScript, MUI 5, Storybook 7 |

MUI 기반 React 공용 디자인 시스템(@imago-cloud/design-system)의 개발 및 릴리즈 관리에 기여.

**주요 성과**
- **React 19 마이그레이션** 수행, **Storybook 7** 호환성 확보 및 패키지 의존성 충돌 해결
- **DatePicker** 컴포넌트 API 확장 (placeholder, onClick handler), **하위 호환성 100%** 유지
- onClose props **프로덕션 버그** 당일 수정/배포, 사용 팀 영향 최소화

---

## 정량적 성과 요약

| 카테고리 | 지표 | 성과 |
|----------|------|------|
| **코드 기여** | 총 커밋 / PR | 1,500+ / 300+ |
| | 총 코드 변경 | 500K+ LOC |
| **빌드 최적화** | Electron 인스톨러 | 854MB → 78MB (91%↓) |
| **코드 품질** | TypeScript 에러 | 688개 → 0개 (100%) |
| | 토큰 관리 코드 | 200줄 → 33줄 (85%↓) |
| | CookieUtil 간소화 | 67% |
| **테스트** | E2E 테스트 케이스 | 80+ TC |
| | E2E 코드 중복 감소 | 93% |
| | 테스트 성능 개선 | 39% |
| **인프라** | 도메인 통합 설계 | 33→10개 (70%↓) |
| | SSL 인증서 통합 설계 | 33→1개 (97%↓) |
| | 환경변수 감소 설계 | 67→5개 (92%↓) |
| **제품** | CAM SW 연동 | 16개+ |
| | 외부 스캐너 연동 | 3개 (Medit, Shining 3D, Connect) |
| | Batch Client 릴리즈 | 13개 (v0.0.1 ~ v1.0.13) |
| | MFE 모듈 | 4개 신규 + 4개 유지보수 |

---

## 핵심 역량

### 프론트엔드 아키텍처 설계
Module Federation 기반 Micro Frontend 아키텍처, FSD(Feature-Sliced Design), Electron IPC 통신 설계 등 25개 앱/30개+ 라이브러리 규모의 NX 모노레포를 설계하고 구축. 5개+ 분산 프로젝트를 통합하고 도메인 33→10개 통합 아키텍처를 설계.

### 테스트 자동화 및 품질 관리
Playwright 80+ E2E TC 구축, AWS EC2 CronJob 일일 자동 실행, Claude CLI 기반 AI 변경 감지 시스템 구축. TDD 기반 개발로 단위 테스트 인프라 구축(Jest, Vitest, MSW).

### CI/CD 및 크로스 플랫폼
Azure DevOps Pipeline 다중 환경 배포 자동화, Windows EV 코드 서명 파이프라인, Electron 크로스 플랫폼 빌드 자동화. 웹(React, Next.js), 데스크톱(Electron), 모바일 반응형 개발.

### 글로벌 서비스 대응
i18next 기반 다국어 시스템 구축, 글로벌 Storage Region 선택, 16개+ 글로벌 CAM 소프트웨어 연동, 3개 외부 스캐너 OAuth 인증 통합.

---

*마지막 업데이트: 2026-02-10*

# 김진완 | Frontend Developer

---

## 자기소개

3년차 프론트엔드 개발자로, AI 기반 치과 CAD/CAM B2B SaaS(DentBird)에서 25개 앱/30개+ 라이브러리 규모의 NX 모노레포 아키텍처를 설계하고, React/TypeScript 기반 웹·데스크톱 애플리케이션을 구축해왔습니다.

- Module Federation 기반 Micro Frontend 아키텍처를 설계하여 독립 배포 체계 확립
- 5개+ 분산 프로젝트를 NX 모노레포로 통합하고, 도메인 33→10개 통합 아키텍처 설계
- Playwright E2E 80+ TC 자동화 및 Kubernetes CronJob 기반 일일 자동 실행 환경 구축
- Electron 데스크톱 앱 전담 개발(13개 버전 릴리즈), 16개+ CAM 소프트웨어 연동 시스템 구축
- Azure DevOps Pipeline 기반 다중 환경 CI/CD 및 Windows EV 코드 서명 자동화

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
| **CI/CD** | Azure DevOps Pipelines, Kubernetes CronJob |
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

이마고웍스 | 2024.06 ~ 현재 | 307 커밋, 275 PR

NX 모노레포 환경에서 플랫폼 아키텍처 개선, E2E 테스트 자동화, 모듈 기능 고도화를 주도.

**기술**: React, TypeScript, Playwright, Nx Monorepo, Azure Pipelines, Datadog, K8s, Kotlin/Spring, TDD

- 분산된 5개+ 프론트엔드 프로젝트를 NX 모노레포로 통합하고, Azure DevOps Pipeline CI/CD 구성 및 Datadog RUM 모니터링 전사 적용
- Playwright 기반 E2E 테스트 프레임워크를 설계하여 80+ 테스트 케이스 구축, Page Object 패턴으로 로그인 중복 코드 93% 감소, 세션 공유 전략으로 테스트 실행 성능 39% 개선
- Kubernetes CronJob 기반 일일 자동 테스트 실행 환경을 구축하고, Claude Code CLI 기반 AI 변경 감지 시스템을 개발하여 커밋 변경사항 자동 분석 → 관련 테스트 선별 실행
- 레거시 모노레포 마이그레이션으로 TypeScript 에러 688→0개 해결, Facade 패턴 토큰 관리 리팩토링으로 코드 200→33줄(85%↓), 통합 도메인 전환 설계로 도메인 33→10개(70%↓)
- Export v5 API 마이그레이션 및 crown-io 모듈 리팩토링, Viewer Module 기능 고도화 (3D 모델 뷰어, Case Share, 모바일 최적화)

---

### 2. Micro Frontend 아키텍처 설계 및 운영

이마고웍스 | 2024.11 ~ 2025.07 (8개월) | 196 커밋 (109 + 87)

iframe 기반 마이크로 프론트엔드 5개 모듈을 운영하며 개별 도메인/빌드 파이프라인 분산의 한계를 경험하고, Module Federation 기반 아키텍처로 전환하여 독립 배포 체계와 중앙화된 모듈 관리를 확립.

**기술**: React 18, TypeScript, Nx, Module Federation, pnpm, VTK.js, TanStack Query, MSW, MUI

- iframe 기반 MFE 5개 모듈(explorer, export, viewer, setting, mobile)을 운영하며 외부 스캐너 3개 서비스(Medit, Shining 3D, Connect) OAuth 연동 및 서브 도메인 마이그레이션을 수행하여 무중단 전환 완료
- iframe 방식의 개별 도메인/빌드 파이프라인 분산 문제를 해결하기 위해 Module Federation 기반 호스트-리모트 아키텍처를 설계하고, FSD 아키텍처 도입 및 ESLint 커스텀 규칙으로 레이어 간 import 방향 강제
- 사내 백오피스(Console Client)용 시스템 알림 FO/BO 등 4개 원격 모듈을 3주 내 신규 개발 (87 커밋, 58K+ LOC)
- 호스트-리모트 간 타입 공유 자동화 스크립트를 구현하고, MSW Mock API 환경을 구축

---

### 3. DentBird Account - B2B SaaS 구독/계정 관리 시스템

이마고웍스 | 2023.09 ~ 2025.07 (1년 10개월) | 160+ 커밋

글로벌 B2B SaaS 수익화를 위한 구독 관리 시스템을 설계하고 프론트엔드 전체를 전담 구축.

**기술**: React 18, TypeScript, TanStack Query, MUI, react-hook-form, i18next, MSW, Jest

- SaaS 구독 관리 시스템 전체(Free/Paid 플랜, 일할 계산 로직, 추가 시트 구매, 결제 방법 관리, 구독 취소/재개, 쿠폰)를 설계/구현하여 수익화 기반 마련
- Seats → Members 시스템 전환 리팩토링을 수행하고, 멤버 초대/권한 관리(다지역 처리 포함)를 구현
- OAuth 회원가입 플로우에 글로벌 Storage Location 선택 및 MFA 인증을 구현하여 글로벌 서비스 대응
- 모바일/태블릿/데스크톱 3단계 반응형 UI 시스템을 설계하고, Jest + MSW 기반 테스트 인프라를 구축

---

### 4. DentBird Linker Desktop - CAM 연동 데스크톱 앱

이마고웍스 | 2024.08 ~ 현재 | 223+ 커밋, 8개 버전 릴리즈

치과 CAM 소프트웨어 연동 Electron 데스크톱 앱을 0→1로 설계/구축하고, Chrome PNA 대응 Custom Protocol 아키텍처 전환을 주도.

**기술**: Electron, React, TypeScript, Vite, Express, Nx Monorepo, Azure Pipelines, Datadog, Jest, draco3d

- Electron + Vite + React 기반 데스크톱 앱을 0부터 설계하고, 내장 Express 서버로 웹↔데스크톱 통신, Config 클래스 기반 동적 소프트웨어 관리 시스템 구축, v1.0.0 ~ v1.0.2 프로덕션 릴리즈
- 16개+ CAM 소프트웨어별 좌표계 차이를 변환 매트릭스 알고리즘으로 해결하고, Export Session 방식 Protocol Handler 아키텍처 설계
- Chrome PNA 규제를 Custom Protocol 아키텍처 전환으로 선제 대응 (WebSocket/Protocol POC → dentbird-linker:// 채택), Electron 인스톨러 854MB → 78MB (91% 감소)
- Azure Pipelines 기반 Windows/macOS 크로스 플랫폼 빌드 자동화 (EV 코드 서명 포함) 및 Datadog RUM/Logs 모니터링 통합

---

### 5. DentBird Batch Client - Electron 데스크톱 앱

이마고웍스 | 2023.12 ~ 2025.01 (13개월) | 347 커밋, 1인 전담 개발

치과 CAD 크라운 배치 처리 Electron 앱을 아키텍처 설계부터 v1.0.13 프로덕션 릴리즈까지 전체 개발을 주도.

**기술**: Electron, React, TypeScript, Recoil, VTK.js, Webpack, Jest, MSW, Datadog

- Electron + React + TypeScript 기반 데스크톱 앱을 초기 세팅부터 구축하고, 라우팅/SNB/페이지 공통 컴포넌트 등 프로젝트 구조를 설계
- Main/Renderer 프로세스 IPC 통신 아키텍처를 설계하고, Deep Link(dentbird://) 커스텀 프로토콜 및 자동 업데이트 시스템을 구현
- Webpack Main 프로세스 번들링, Windows/Mac 코드 서명, 환경별(dev/qa/prod) 빌드 분리, Artifact 버전 관리까지 빌드/배포 파이프라인을 구축
- 테스트 문화가 없는 환경에서 Jest + MSW 기반 단위 테스트 인프라를 도입하고, 주요 모듈 테스트 코드를 작성
- Datadog RUM 연동(환경별 설정 분리, 빌드 시 환경변수 주입) 및 Electron Crash Reporter를 구축하여 프로덕션 에러 추적 체계 확립

---

### 6. 기업 랜딩 페이지 풀스택 개발

이마고웍스 | 2023.09 ~ 2025.10 (약 2년) | 568 커밋

Next.js 기반 기업 랜딩 페이지 v3 전면 리뉴얼과 관리자 Backoffice, 서버 API를 함께 개발한 풀스택 프로젝트.

**기술**: Next.js, React, TypeScript, i18next, Node.js, Fastify, MongoDB

- Next.js 기반 10+ 페이지 신규 구현 및 AOS 애니메이션 시스템 통합
- i18next + TypeScript 기반 타입 안전한 다국어 시스템을 설계하여 3개 언어(한/영/일) 지원, i18next-resources-for-ts 패키지 도입으로 타입 자동 생성
- LCP 이미지 프리로드, Next.js Image 최적화, 캐싱 전략 적용으로 Lighthouse 성능 점수 개선
- Backoffice 조직 관리 시스템(Groups/Teams/Members 3계층 CRUD)을 1주 53 커밋으로 구현하고, Node.js/Fastify/MongoDB 기반 일괄 수정 API를 설계/개발

---

### 7. Imago Cloud Design System

이마고웍스 | 2025.04 ~ 2025.08 (4개월) | 9 커밋

MUI 기반 React 공용 디자인 시스템(@imago-cloud/design-system)의 개발 및 릴리즈 관리에 기여.

**기술**: React 18/19, TypeScript, MUI 5, Storybook 7

- React 19 마이그레이션을 수행하여 Storybook 7 호환성을 확보하고 패키지 의존성 충돌 해결
- DatePicker 컴포넌트 API를 확장(placeholder, onClick handler)하여 하위 호환성 100% 유지
- onClose props 프로덕션 버그를 당일 수정/배포하여 사용 팀 영향 최소화

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
| | MFE 모듈 | 4개 신규 + 5개 유지보수 |

---

## 핵심 역량

### 프론트엔드 아키텍처 설계
Module Federation 기반 Micro Frontend 아키텍처, FSD(Feature-Sliced Design), Electron IPC 통신 설계 등 25개 앱/30개+ 라이브러리 규모의 NX 모노레포를 설계하고 구축. 5개+ 분산 프로젝트를 통합하고 도메인 33→10개 통합 아키텍처를 설계.

### 테스트 자동화 및 품질 관리
Playwright 80+ E2E TC 구축, K8s CronJob 일일 자동 실행, Claude CLI 기반 AI 변경 감지 시스템 구축. TDD 기반 개발로 단위 테스트 인프라 구축(Jest, Vitest, MSW).

### CI/CD 및 크로스 플랫폼
Azure DevOps Pipeline 다중 환경 배포 자동화, Windows EV 코드 서명 파이프라인, Electron 크로스 플랫폼 빌드 자동화. 웹(React, Next.js), 데스크톱(Electron), 모바일 반응형 개발.

### 글로벌 서비스 대응
i18next 기반 다국어 시스템 구축, 글로벌 Storage Region 선택, 16개+ 글로벌 CAM 소프트웨어 연동, 3개 외부 스캐너 OAuth 인증 통합.

---

*마지막 업데이트: 2026-02-10*

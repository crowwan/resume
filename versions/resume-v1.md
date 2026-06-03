# 김진완 (Jinwan Kim) | Frontend Developer

> **제품의 구조와 품질을 끝까지 책임지는 프론트엔드 개발자**
> React·TypeScript를 기반으로 화면 구현을 넘어 Electron 데스크톱, 빌드·배포 인프라, 테스트 자동화까지 — 제품이 안정적으로 돌아가는 구조를 만듭니다.

- [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023.09부터 AI 기반 치과 CAD/CAM SaaS(DentBird)에서, 화면 구현을 넘어 **제품의 구조와 품질을 책임지는** 프론트엔드 개발자로 일해왔습니다.

- **범위** — FE를 중심으로 Electron 데스크톱 앱, 빌드·배포 인프라, 일부 백엔드까지 제품의 처음부터 끝까지 다룹니다.
- **구조** — 분산된 앱을 모노레포로 통합하고, 공통 모듈을 MFE로, 환경 설정을 런타임으로 분리하는 등 플랫폼 구조를 설계합니다.
- **판단** — Chrome LNA 우회 방안 20여 가지를 분석해 장기 안정성 기준으로 선택하는 등, 기술 결정의 근거를 남기고 팀과 공유합니다.
- **효율** — 커밋 단위 격리 재현 환경과 E2E·AI 자동 테스트로 디버깅과 릴리스의 신뢰성을 높입니다.

---

## 핵심 역량

- **플랫폼 아키텍처**: NX 모노레포 통합, Micro Frontend, 런타임 환경 분리, 도메인 설계
- **데스크톱**: Electron 앱 0→1 설계·프로덕션 릴리스, 자동 업데이트·코드 서명 인프라
- **품질 자동화**: Playwright E2E, 커밋 단위 격리 재현 환경, AI 기반 변경 감지 테스트
- **제품 도메인**: B2B SaaS 구독/결제 FE 전담, 글로벌 i18n·멀티리전

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(DentBird)의 프론트엔드 전반을 설계·구축. 모노레포 통합, MFE 전환, 런타임 인프라, 품질 자동화 등 플랫폼 기술 결정에 참여·주도.

---

## 프로젝트

### 1. DentBird Solutions — 플랫폼 인프라 & 품질 자동화

런타임 환경 분리와 커밋 단위 격리 재현, E2E·AI 자동 테스트로 배포 안정성과 디버깅·릴리스 신뢰성을 끌어올렸습니다.

- 환경변수를 **빌드타임에서 런타임 주입으로 전환**하는 설계에 참여하여, 빌드 결과물 1개를 dev·qa·prod에 환경변수만 바꿔 배포하는 구조를 확립했습니다. **배포 직후 핫픽스를 반복하던 문제를 해소**하고 환경별로 따로 걸리던 빌드를 단일 빌드로 줄였습니다.
- 이 런타임 분리를 기반으로 **특정 커밋 시점의 클라이언트+서버를 Docker로 재현하는 격리 환경을 주도 구축**하고, qa/prod의 데이터를 복제해 즉시 재현하도록 만들어 **신뢰도 높은 디버깅 환경**을 마련했습니다.
- **Playwright 기반 E2E 프레임워크를 주도 구축**하여 Page Object 패턴으로 로그인 중복 코드를 **93% 줄이고**, 세션 공유로 테스트 실행을 **39% 개선**했습니다.
- **EC2에 Claude를 띄워 커밋 변경을 분석 → 관련 E2E를 자동 선별·실행 → Teams로 보고하는 AI 변경 감지 시스템**을 구축했습니다.
- 레거시 모노레포 통합 시 **TypeScript 에러 688 → 0**, Facade 패턴으로 토큰 관리 코드 **200 → 33줄(85%↓)** 로 정리했습니다.

### 2. DentBird Linker — CAM 연동 Electron 데스크톱 앱

웹 브라우저와 로컬 CAM 소프트웨어를 잇는 Electron 앱을 0→1로 설계하고, Chrome LNA 규제에 Custom Protocol로 선제 대응했습니다.

- Electron + Vite + React 아키텍처를 설계하고 **1개월간 113커밋**으로 구축, v1.0.3까지 프로덕션 릴리스했습니다.
- Chrome **Local Network Access(LNA)** 정책으로 웹→로컬 통신이 차단되는 문제에, **20여 가지 우회 방안을 분석한 뒤 장기 안정성 기준으로 Custom Protocol을 채택**했습니다(WebSocket은 향후 LNA 재차단 우려로 탈락).
- **16개+ CAM 소프트웨어**별 좌표계 차이를 변환 매트릭스 알고리즘으로 해결하고, DRC→STL 실시간 변환 파이프라인을 구축했습니다.

### 3. DentBird Account — B2B SaaS 구독/계정 시스템

크레딧 기반에서 **구독제로 전환**하는 결제·권한 도메인의 프론트엔드를 전담 설계·구축했습니다.

- 플랜 업그레이드, 시트 구매(일할 계산), 구독 취소/재개, 쿠폰, 결제수단, 결제내역을 **전담 구현**했습니다.
- ErrorBoundary 기반 선언적 에러 처리와 커스텀 비즈니스 에러 핸들링을 도입하고, msw 기반 단위·통합 테스트를 붙였습니다.
- (성장) 디자인 패턴·FSD를 적극 도입해봤으나, **"패턴은 검증된 공통 문제의 해법인데 우리가 풀려던 문제는 그게 아니었다"**는 판단 기준을 얻었습니다 → 기술 도입은 문제 해결이, 그것도 우선순위 높은 문제가 먼저라는 원칙으로 이어졌습니다.

### 4. Micro Frontend — 공통 모듈 통합

4개 서비스가 공유하는 기능(setting/export/explorer/viewer)을 iframe 런타임 통합으로 모듈화하고, 통합 전략을 상황에 맞게 재설계했습니다.

- 라이브러리 배포가 반복 배포 문제를 못 푸는 것을 확인하고 **iframe 런타임 통합을 선택**했습니다. Module Federation은 notification·console-client에 직접 적용해본 뒤 초기 설정·소비처 부담을 이유로 이 건에선 제외했습니다.
- 모노레포 통합 후 빌드타임 통합으로 옮겼다가, 배포 부담이 커지자 다시 iframe 런타임 통합으로 회귀하며 **"통합 전략에 정답 기술은 없고 조직·배포 제약에 맞는 선택이 있을 뿐"**임을 체득했습니다.

### 5. Electron 빌드·배포·자동 업데이트 인프라 (Batch·Linker 공통)

담당자 PC·인프라팀에 의존하던 빌드를 self-hosted 파이프라인으로 재설계하고 GitHub Actions 이관을 주도했습니다.

- 빌드 시간을 **약 20분 → 약 6분**으로 단축하고(직접 비교), 담당자·코드사인 USB·인프라팀 의존을 제거했습니다.
- electron-builder의 `BitbucketPublisher`를 직접 분석·패치(hostname·timeout·인증)하고, QA/PROD 채널을 분리해 잘못된 빌드 수신 사고를 방지했습니다.

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
| **State** | TanStack Query, Recoil, Zustand |
| **UI** | MUI, Emotion, styled-components, Storybook |
| **Build/Arch** | NX, pnpm, Module Federation, FSD, 런타임 Config |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **CI/CD·Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3), Nginx |
| **Monitoring** | Datadog RUM/Logs |
| **Backend(부분)** | Node.js, Fastify, Kotlin/Spring(연동), MongoDB |

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

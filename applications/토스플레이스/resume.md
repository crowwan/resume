# 김진완 (Jinwan Kim) | Frontend Developer

> **웹 기술로 하드웨어 위에서 도는 소프트웨어를 만드는 프론트엔드 개발자**
> React·TypeScript·Electron으로 웹↔로컬 소프트웨어 연동을 설계·구축하고, 빌드·배포 인프라와 품질 자동화까지 — 제품이 현장에서 안정적으로 돌아가는 구조를 만듭니다.

- [포트폴리오](https://crowwan.github.io/resume/portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023.09부터 AI 기반 치과 CAD/CAM SaaS(Dentbird)에서, 화면 구현을 넘어 **웹과 로컬 소프트웨어를 잇는 데스크톱 앱**을 직접 설계·구축해 왔습니다. 토스플레이스가 POS·키오스크라는 하드웨어 위에서 웹 기술로 소프트웨어를 돌리는 것과, 제가 풀어온 문제는 같은 계열이라고 생각합니다.

- **소프트웨어 연동** → 웹↔로컬 통신을 막는 브라우저 정책을 우회하고, **이질적인 외부 CAM들을 하나의 인터페이스로 추상화**
- **판단** → LNA 우회 20여 방안을 탐색해 장기 안정성 기준으로 Custom Protocol 채택
- **구조** → 분산된 앱 NX 모노레포 통합 · 공통 모듈 MFE 분리 · 환경설정 런타임 분리
- **효율·품질** → Electron 빌드 20분대 → 6분 · 격리 재현 환경 · AI 변경 감지로 회귀 조기 탐지

---

## 핵심 역량

- **데스크톱·소프트웨어 연동**: Electron 설계·프로덕션 릴리스, 웹↔로컬 통신(Custom Protocol, Deep Link), 외부 CAM 연동·좌표 변환 파이프라인
- **빌드·배포 인프라**: GitHub Actions self-hosted 이관, 코드 서명·자동 업데이트, 빌드 20분대 → 6분
- **플랫폼 아키텍처**: NX 모노레포 통합, Micro Frontend, 런타임 환경 분리
- **품질 자동화**: Playwright E2E, 커밋 단위 Docker 격리 재현, AI 기반 변경 감지 테스트

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(Dentbird)의 프론트엔드 전반을 설계·구축했습니다. 웹↔로컬 소프트웨어 연동, 모노레포 통합, Micro Frontend 전환, 빌드·배포 인프라, 품질 자동화 등 플랫폼 기술 결정을 주도했습니다.

---

## 프로젝트

### 웹↔로컬 CAM 연동 Electron 앱 — Dentbird Linker

클라우드에서 설계한 결과물을 사용자 PC의 CAM 소프트웨어로 보내야 했지만, 자체 로컬 서버가 없는 CAM까지 연동해야 했고 Chrome의 Local Network Access(LNA) 정책이 웹→로컬 통신을 막았습니다. Electron 앱을 설계·구축하고 소프트웨어 연동부터 빌드 인프라까지 책임졌습니다.

- 브라우저-로컬 통신을 **Custom Protocol 중간 레이어로 우회**하도록 재설계 — 기존 로컬 서버 방식을 걷어냄
- 이질적인 외부 CAM들의 좌표계·전달 방식 차이를 **하나의 변환·연동 인터페이스로 추상화**
- 명확한 스펙이 없어 기존 기능을 특성화 테스트로 고정하고 구현으로 통과 — 버그 수정이 곧 스펙
- Batch·Linker 공통 빌드 인프라를 self-hosted로 재설계해 빌드를 20분대 → 6분으로 *(팀 작업, 본인 최다 기여)*
- **판단**: AI로 LNA 우회 20여 방안을 탐색해 장기 안정성 기준으로 Custom Protocol 채택. WebSocket은 구현은 빠르나 곧 LNA 적용 대상이라 재차단 우려로 탈락. Custom Protocol은 HTTP 요청이 아니어서 LNA 적용 대상이 아니고 정책 변화에 덜 취약

### 공통 모듈 통합 — Micro Frontend

4개 서비스가 공유하는 기능(설정·내보내기·탐색기·뷰어)을 런타임 통합으로 모듈화하고, "정답 기술"을 찾는 대신 조직·배포 제약에 맞는 통합 전략을 거듭 다시 골랐습니다.

- 공통 기능 변경을 **각 서비스 반복 수정에서 모듈 한 곳 수정으로** 좁힘 — iframe + postMessage 런타임 통합
- Module Federation은 직접 도입해본 뒤 소비처 설정 부담으로 이 건엔 제외, 다른 서비스엔 적용
- 사내 3D 라이브러리→Three.js 전환(팀 주도)에서 **Viewer 렌더링 정합·회귀 수정을 담당** *(현재 진행)*
- **판단**: 빌드타임 통합 → 배포 부담 증가 → 다시 iframe 회귀. "정답 기술 없이 조직·배포 제약에 맞춰 재선택"

### 플랫폼 인프라 & 품질 자동화 — Dentbird Solutions

모노레포 이관 중 **빌드 환경 설정이 운영에 새어들어 배포되던 문제**부터, 격리 재현·E2E·AI 자동 테스트까지 플랫폼 품질 기반을 만들었습니다.

- 환경설정을 빌드타임 주입에서 **런타임 주입으로 전환** → 빌드 환경 설정이 운영에 새어들던 사고를 없애고 같은 번들 1개를 전 환경 공용으로
- 커밋 시점의 클라이언트+서버를 **Docker로 격리 재현**, 상태 보존 서비스 공유로 셋업 단축
- 흩어진 E2E를 모노레포로 통합, Page Object·세션 자동 관리로 중복 제거
- **AI 변경 감지 E2E**: 커밋 변경을 분석해 관련 E2E만 선별·실행하고 결과를 Teams로 보고
- **판단**: "격리하기 쉬운 환경"을 먼저 깔아 그 위에 자동화를 확장. AI 변경 감지는 운영 한계(보안·안정성)까지 겪고 재편 중

### B2B SaaS 구독/계정 시스템 — Dentbird Account

크레딧제에서 구독제로 전환하는 결제·권한 도메인 FE를 전담해 글로벌 B2B 수익화 기반을 마련했습니다. 결제처럼 틀리면 안 되는 흐름의 신뢰성을 설계로 지켰습니다.

- 플랜·시트·취소/재개·쿠폰·결제수단까지 **구독 워크플로우 전체 구현**
- 명령형 방어 코드를 **ErrorBoundary 선언적 처리로 이관**하고, 결제 정보를 못 받아도 핵심 흐름은 살아남도록 Fault Tolerance 설계
- 서버 비즈니스 에러를 커스텀 에러로 코드별 분기, FSD 레이어 규칙 직접 설계
- **판단**: FSD·패턴을 과적용해보고 — 폴더 위치 합의 비용, 팀원 이해 분기 — "기술 도입은 우선순위 높은 문제부터"라는 기준 확립

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
| **State / UI** | TanStack Query, Recoil · MUI, Emotion |
| **Build / Arch** | NX, pnpm, Module Federation, 런타임 Config |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08
</content>
</invoke>

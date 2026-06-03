# 김진완 (Jinwan Kim) | Frontend Developer

> **왜 그렇게 결정했는지 설명할 수 있는 프론트엔드 개발자**
> 모노레포 통합·Micro Frontend·데스크톱 앱을, 작업 나열이 아니라 "어떤 문제를 어떤 대안 중에 왜 그렇게 풀었나"로 만들어온 개발자입니다.

- [포트폴리오](https://crowwan.github.io/resume/portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023.09부터 AI 기반 치과 CAD/CAM SaaS(DentBird)에서 일하며, 여러 서비스가 공유하는 공통 모듈을 어떻게 통합할지를 두고 **라이브러리 배포 → iframe → 빌드타임 → 다시 iframe**까지 전략을 거듭 다시 골랐습니다. 그 과정에서 배운 건 "정답 기술은 없고, 조직과 배포 제약에 맞는 선택이 있을 뿐"이라는 점입니다. 채널톡이 모노레포 도구를 고를 때 Nx 대신 yarn workspace와 TypeScript Project References를 택한 것처럼, 저도 기술을 고를 때 "왜 이게 우리 문제에 맞는가"를 먼저 따지는 편입니다.

- **판단** — 기술을 도입할 때 탈락한 대안과 그 이유까지 설명할 수 있게, 트레이드오프를 남깁니다.
- **구조** — 분산된 앱을 NX 모노레포로 통합하고, 공통 모듈의 결합도를 낮춰 독립 배포 단위로 분리합니다.
- **데스크톱** — Electron 앱을 0→1로 설계하고, 빌드·배포 파이프라인을 재설계해 생산성을 끌어올립니다.
- **품질** — 격리 재현·E2E·AI 변경 감지로 품질을 자동화합니다.

---

## 핵심 역량

- **플랫폼 아키텍처**: NX 모노레포 통합, 공통 모듈 런타임 통합(iframe·postMessage / console-client에 Module Federation), 런타임 환경 분리
- **데스크톱**: Electron 앱 0→1 설계·릴리스, 빌드·자동 업데이트 인프라(self-hosted, 20→6분)
- **품질 자동화·DX**: Playwright E2E, 커밋 단위 Docker 격리 재현, AI 기반 변경 감지 테스트
- **제품 도메인**: B2B SaaS 구독/결제 FE 전담, 글로벌 멀티테넌트·다국어

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

---

## 프로젝트

### 1. 플랫폼 아키텍처 — 모노레포 통합 & Micro Frontend *(판단·구조)*

4개 서비스(cloud·crown·modeler·milling)가 공유하는 공통 기능 4개가, 하나 바뀔 때마다 **각 서비스에 같은 수정을 반복하고 전부 배포**해야 하는 비용이 문제였습니다. 이걸 푸는 통합 전략을 여러 번 다시 골랐습니다.

- 먼저 컴포넌트를 **라이브러리로 배포**해봤지만, 각 서비스가 버전을 올리고 다시 배포해야 해서 풀려던 문제가 그대로 남았습니다. 그래서 런타임 통합으로 방향을 틀어 **iframe + postMessage(typed, same-origin)** 를 택했습니다.
- **Module Federation**은 notification 기능에서 직접 도입해본 경험상 초기 설정이 복잡하고 모노레포 밖 소비처에서 설정 부담이 커서 이 건에선 제외했습니다 — 몰라서가 아니라 써보고 트레이드오프로 뺀 판단이며, console-client에는 별도로 적용했습니다.
- 분산된 앱을 **NX 모노레포로 통합**한 뒤에는 빌드타임 통합으로 옮겼다가, **통합 배포가 실제로는 잘 이뤄지지 않고 사내 배포 프로세스가 짧은 주기 배포를 어렵게 바뀌자 다시 iframe 런타임 통합으로 회귀**했습니다. 이번엔 모듈별 도메인 대신 same-origin으로 서빙해 CORS·인증 공유를 단순화했습니다.
- 통합 과정에서 **TypeScript 에러 688 → 0**, Facade 패턴으로 토큰 관리 코드 **200 → 33줄(85%↓)** 로 정리했습니다.

### 2. DentBird 데스크톱 앱 & 빌드·배포 인프라 *(데스크톱)*

상담사가 쓰는 데스크 앱처럼, 저도 사용자 현장에서 도는 Electron 데스크톱 앱을 0→1로 만들고 그 빌드·배포까지 책임졌습니다.

- 웹↔로컬 통신을 막는 Chrome **LNA 정책**에 대응해 HTTPS 로컬 서버·WebSocket·Custom Protocol 등을 비교하고, **장기 안정성 기준으로 Custom Protocol을 채택**해 한 달여 만에 v1.0.3까지 릴리스했습니다. 12종 외부 소프트웨어를 좌표 변환으로 연동했습니다.
- 빌드가 담당자 PC·인프라팀(코드사인 USB)에 묶여 있던 구조를 self-hosted 빌드머신으로 재설계하고, **Azure Pipelines → GitHub Actions 이관을 주도**해 빌드 시간을 **20분 → 6분(직접 비교)** 으로 줄였습니다. electron-builder의 Publisher 클래스를 직접 분석해 버그·인증을 패치하는 등 라이브러리 내부까지 파고들었습니다.

### 3. 품질 자동화 — 격리 재현 & AI 변경 감지 *(품질·DX)*

- 특정 커밋 시점을 Docker로 재현하는 **격리 환경을 주도 구축**해 환경 차이로 깨지던 디버깅·E2E를 안정화했습니다.
- Playwright E2E를 주도 구축하며 반복되던 **로그인 셋업 코드(약 60줄)** 를 공통 Page Object 호출로 대체해 **중복을 93% 제거**하고, 세션 공유로 스위트 실행을 **18분 → 11분(39%↓)** 단축했습니다.
- EC2에 Claude를 띄워 커밋 변경을 분석하고 **관련 E2E만 선별 실행**해 보고하는 AI 변경 감지 시스템을 구축했습니다.

### 4. DentBird Account — B2B SaaS 구독/결제 FE *(제품 도메인)*

크레딧 기반에서 구독제로 전환하는 결제·권한 도메인의 프론트엔드를 전담 설계·구축했습니다. 플랜·시트 구매, 구독 취소/재개, 쿠폰, 결제수단까지 결제 워크플로우 전체를 구현하고, 금액이 어긋나기 쉬운 흐름의 정확성을 검증과 테스트로 확보했습니다. B2B SaaS·글로벌 멀티테넌트·다국어 도메인을 다뤄왔습니다.

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript (ES6+) |
| **Frontend** | React 18/19, Next.js |
| **State / UI** | TanStack Query, Recoil · MUI, Emotion |
| **Build / Arch** | NX, pnpm, Module Federation, 런타임 Config |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update) |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

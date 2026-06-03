# 김진완 (Jinwan Kim) | Frontend Developer

> **웹 기술로 하드웨어 위에서 도는 소프트웨어를 만드는 프론트엔드 개발자**
> Electron 데스크톱과 웹↔로컬 디바이스 연동을 0→1로 설계하고, 빌드·배포 인프라와 품질 자동화까지 — 제품이 현장에서 안정적으로 돌아가는 구조를 만듭니다.

- [포트폴리오](https://crowwan.github.io/resume/portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023.09부터 AI 기반 치과 CAD/CAM SaaS(DentBird)에서 일하며, **웹과 로컬 디바이스·소프트웨어를 잇는 데스크톱 앱**을 직접 설계·구축했습니다. 브라우저가 막는 로컬 통신을 Custom Protocol로 뚫고, 12종의 외부 CAM 소프트웨어를 연동하고, 그 앱의 빌드·배포 인프라까지 책임졌습니다. 토스플레이스가 POS·키오스크라는 하드웨어 위에서 웹 기술로 소프트웨어를 돌리는 것과, 제가 풀어온 문제는 같은 계열이라고 생각합니다.

- **디바이스 연동** — 웹↔로컬 통신을 막는 브라우저 정책을 우회하고, 외부 하드웨어/소프트웨어를 좌표·프로토콜 수준에서 연동합니다.
- **멀티플랫폼·배포** — Electron 앱을 Windows/Mac 크로스 플랫폼으로 릴리스하고, 빌드·배포 파이프라인을 재설계해 배포 속도를 끌어올립니다.
- **구조** — 분산된 앱을 모노레포로 통합하고, 공통 모듈을 독립 배포 단위(MFE)로, 환경 설정을 런타임으로 분리합니다.
- **품질** — 격리 재현 환경과 E2E·AI 자동 테스트로 결제처럼 틀리면 안 되는 흐름의 신뢰성을 지킵니다.

---

## 핵심 역량

- **데스크톱·디바이스 연동**: Electron 0→1, 웹↔로컬 통신(Custom Protocol, Deep Link), 외부 CAM 소프트웨어 12종 연동, 좌표 변환 파이프라인
- **빌드·배포 인프라**: GitHub Actions self-hosted, 코드 서명·자동 업데이트, blue-green, 빌드 20분 → 6분
- **플랫폼 아키텍처**: NX 모노레포 통합, Micro Frontend, 런타임 환경 분리
- **품질 자동화**: Playwright E2E, 커밋 단위 Docker 격리 재현, AI 기반 변경 감지 테스트

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

---

## 프로젝트

### 1. DentBird Linker — 웹과 로컬 CAM을 잇는 Electron 데스크톱 앱 *(디바이스 연동)*

클라우드에서 설계한 결과물을 사용자 PC의 CAM 소프트웨어로 보내야 했지만, Chrome의 **Local Network Access(LNA)** 정책이 웹→로컬 통신을 차단했습니다. 권한 팝업을 놓친 사용자의 CS 문의가 다수 발생했고, "지금 되는 방법"을 택하면 정책 강화 시 다시 막히는 구조였습니다.

- HTTPS 로컬 서버·WebSocket·Chrome Extension·WebRTC·mDNS 등을 비교한 뒤, **구현 속도가 아니라 장기 안정성을 기준으로 Custom Protocol을 채택**했습니다. WebSocket은 빠르지만 향후 LNA 적용 대상이라 다시 막힐 단기 해법으로 판단해 탈락시켰고, Custom Protocol은 HTTP 요청이 아니어서 LNA 영향을 받지 않는 영구적 우회로 선택했습니다.
- Electron + Vite + React 아키텍처를 설계해 한 달여 만에 v1.0.3까지 프로덕션 릴리스하고, `dentbird-linker://` 딥링크 통신(앱 미실행 시 URL 저장 후 처리)과 draco3d 기반 **DRC → STL 실시간 변환 파이프라인**을 구축했습니다.
- **12종 CAM 소프트웨어**별 좌표계 차이를 변환 매트릭스 알고리즘으로 흡수하고, CAM별 정합 회귀 테스트로 검증해 어떤 소프트웨어로 내보내도 좌표 오류 없이 동작하도록 만들었습니다.
- INI/XML 생성·HTTP 통신 로직을 TDD로 구현해 단위 테스트 18개로 가드하고, Datadog RUM/Logs로 Main/Renderer 프로세스 에러를 추적했습니다.
- Custom Protocol은 HTTP 요청이 아니어서, 이후 LNA 정책이 강화돼도 **재차단 없이 동작**합니다 — 권한 팝업을 놓쳐 발생하던 통신 차단 CS 유형을 구조적으로 없앤 선택이었습니다.

### 2. Electron 빌드·배포·자동 업데이트 인프라 *(멀티플랫폼·배포)*

두 Electron 앱의 빌드가 처음엔 담당자 PC와 인프라팀(코드사인 USB)에 묶여 있어, 담당자가 없으면 빌드가 멈추고 요청이 한 사람에게 몰렸습니다.

- Windows는 본인 PC를 self-hosted 빌드머신으로, Mac은 notarize 파일을 DevOps Library에 등록해 자동화하고, **Azure Pipelines → GitHub Actions self-hosted 러너로 이관을 주도**해 빌드 시간을 **20분 → 6분(직접 비교)** 으로 줄였습니다.
- 기본 Publisher가 환경에 맞지 않자 electron-builder의 `BitbucketPublisher` 클래스를 직접 분석해 hostname 버그·timeout·인증을 패치했고, 운영에는 유지보수 부담을 고려해 yaml 커스텀으로 단순화했습니다.
- **코드 서명 없이 빌드한 아티팩트를 나중에 서명하면 checksum이 바뀌어 자동 업데이트가 깨진다**는 점을 직접 부딪혀, 코드 서명을 빌드 과정에 포함하는 구조로 정리했습니다. blue-green 배포에서 green 검증 자동화는 데스크톱 앱 특성상 운영 비용이 커, 그 가치를 따져 의도적으로 보류한 상태입니다.

### 3. 플랫폼 아키텍처 — 모노레포 통합 & Micro Frontend *(구조)*

4개 서비스가 공유하는 공통 기능을 모듈화하면서, **"정답 기술"을 찾는 대신 조직·배포 제약에 맞는 통합 전략을 거듭 다시 고른** 과정입니다.

- 공통 기능을 라이브러리로 배포했더니 각 서비스가 버전 업·재배포를 반복해야 해, **iframe + postMessage 런타임 통합**으로 전환했습니다. Module Federation은 직접 도입해본 뒤 소비처 설정 부담으로 이 건에선 제외하고, console-client에는 별도로 적용했습니다.
- 모노레포 통합 후 빌드타임 통합으로 옮겼다가 **통합 배포가 실제로는 잘 안 되고 배포 부담이 커지자 다시 iframe 런타임 통합으로 회귀**하며, "통합 전략에 정답 기술은 없고 조직·배포 제약에 맞는 선택이 있을 뿐"임을 체득했습니다.
- 환경변수를 빌드타임에서 런타임 주입으로 전환하는 구조를 공동 설계해, 빌드 결과물 1개를 환경변수만 바꿔 dev·qa·prod에 배포하도록 만들고 배포 직후 핫픽스 반복을 해소했습니다.

### 4. 품질 자동화 — 격리 재현 & AI 변경 감지 *(품질)*

- 특정 커밋 시점의 클라이언트+서버를 Docker로 재현하는 **격리 환경을 주도 구축**하고, qa/prod 데이터를 복제해 즉시 재현하도록 만들어 환경 차이로 깨지던 디버깅·E2E를 안정화했습니다.
- Playwright E2E 프레임워크를 주도 구축하며, 반복되던 로그인 셋업 코드를 공통 Page Object로 대체해 **중복을 93% 제거**하고, 세션 공유로 스위트 실행 시간을 **18분 → 11분(39%↓)** 단축했습니다.
- EC2에 Claude를 띄워 커밋 변경을 분석하고 **관련 E2E만 선별 실행**해 Teams로 보고하는 AI 변경 감지 시스템을 구축해, 회귀를 같은 PR 안에서 조기에 잡도록 했습니다.

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

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

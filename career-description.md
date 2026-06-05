# 경력기술서 — 김진완 (Jinwan Kim)

> **제품의 구조와 품질을 책임지는 프론트엔드 개발자**
> React·TypeScript를 기반으로 화면 구현을 넘어 Electron 데스크톱, 빌드·배포 인프라, 테스트 자동화까지 — 제품이 안정적으로 돌아가는 구조를 만듭니다.

**Frontend Developer** | 이마고웍스 (ImagoWorks) · Dentbird 1 Team | 2023.09 ~ 현재

- [이력서](resume.html) · [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 경력 요약

2023.09부터 AI 기반 치과 CAD/CAM SaaS(Dentbird)에서 화면 구현을 넘어 **제품의 구조와 품질을 책임지는** 프론트엔드 개발자로 일해왔습니다.

- **구조**를 설계합니다 → 분산된 앱을 NX 모노레포로 통합 · 공통 모듈 MFE 분리 · 환경설정 런타임 분리
- **판단**으로 결정합니다 → LNA 우회 20여 방안을 탐색해 장기 안정성 기준으로 Custom Protocol 채택 등, 대안·트레이드오프를 기록·공유
- **효율**을 끌어올립니다 → Electron 빌드 20분대 → 6분 · 커밋 단위 격리 재현 · AI 변경 감지로 회귀 조기 탐지
- **범위**를 넓힙니다 → FE + Electron 데스크톱 + 빌드·배포 인프라 + 일부 백엔드 풀스택

---

## 핵심 역량

- **플랫폼 아키텍처**: NX 모노레포 통합, Micro Frontend(iframe·Module Federation), 런타임 환경 분리
- **품질 자동화**: Playwright E2E, 커밋 단위 Docker 격리 재현, AI 기반 변경 감지 테스트
- **데스크톱**: Electron 앱 설계·프로덕션 릴리스, 빌드·자동 업데이트·코드 서명 인프라
- **제품 도메인**: B2B SaaS 구독/결제 FE 전담, 글로벌 멀티테넌트·다국어

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(Dentbird)의 프론트엔드 전반을 설계·구축했습니다. 모노레포 통합, Micro Frontend 전환, 런타임 인프라, 품질 자동화 등 플랫폼 기술 결정을 주도했습니다.

---

## 프로젝트 1. 플랫폼 인프라 & 품질 자동화 — Dentbird Solutions
`2024.06 ~ 현재` · 런타임 분리 공동설계 / 격리 재현·E2E·AI 자동화 주도

모노레포 이관 중, dev·qa에선 멀쩡하던 빌드가 **prod에 dev 환경변수가 박힌 채 배포**돼 핫픽스를 다시 배포하는 일이 반복됐고, 특정 커밋 시점을 재현하기 어려워 환경 차이로 E2E가 쉽게 깨졌습니다.

**▸ 기여**
- prod에 dev 환경변수가 박힌 채 배포되던 사고를 진단해 **환경변수를 런타임 주입으로 전환**(팀 공동 설계) → 같은 번들 1개를 전 환경 공용으로 만들어, 그 위에 격리 재현을 쌓을 토대 마련
- **커밋 단위로 그 시점의 클라이언트+서버를 Docker로 격리 재현**해, 환경 차이로 깨지던 디버깅·E2E를 안정화
- 공용 상태 보존 서비스를 1세트만 공유하는 모델로 환경 셋업을 단축 *(약 32분 → 3~5분 · 팀과 합의)* — 로컬 의존성 에뮬레이션·회귀 가드 포함
- 흩어져 있던 E2E를 **모노레포로 통합** 주도, Page Object 패턴·세션 자동 관리로 중복 제거
- **AI 변경 감지 E2E**: EC2에 띄운 Claude가 커밋 변경을 분석 → 관련 E2E만 선별·실행 → Teams 보고 *(주기적 무인 검증)*

**▸ 판단·지속성**
- 격리 환경을 바로 만들지 않고, 런타임 분리·앱 통합으로 "격리하기 쉬운 환경"을 먼저 깔고 그 위에 쌓는 순서를 택함
- AI 변경 감지는 보안 정책·스케줄 안정성 한계를 겪고 현재 로컬 주기 실행 방식으로 재편 중 — 구축·운영·한계까지 겪어본 영역

`기술` React · TypeScript · Playwright · Docker · NX · AWS(EC2·S3) · Claude Code CLI · Datadog

---

## 프로젝트 2. 웹↔로컬 CAM 연동 Electron 데스크톱 앱 — Dentbird Linker
`2024.07 ~ 2025.12` (이후 Solutions로 통합) · 아키텍처 설계·구현 주도

Dentbird에서 만든 케이스를 외부 CAM 소프트웨어로 보내려면 다운로드·압축 해제·수동 투입을 거쳐야 했고, **자체 로컬 서버가 없는 CAM은 연동 자체가 불가**했습니다. 게다가 Chrome **LNA(Local Network Access)** 정책이 웹→로컬 통신을 차단해, 권한 팝업을 놓친 사용자의 CS 문의가 급증했습니다.

**▸ 기여**
- 케이스를 외부 CAM에 자동 투입하는 Electron 앱을 **프로덕션으로 서비스화** (초기 검증은 타인, 프로덕션화는 본인)
- **Custom Protocol 중간 레이어**로 재설계: 브라우저가 앱을 깨워 세션을 위임 → 앱이 서버에서 직접 파일을 받아 변환 후 CAM 실행 *(기존 로컬 서버 방식 폐기)*
- 이질적인 외부 CAM들의 좌표계·파일 전달 방식 차이를 **하나의 변환·연동 인터페이스로 추상화**
- 명확한 스펙이 없어, 기존 기능을 **특성화 테스트로 고정**하고 새 구현으로 통과시키는 전략 — 버그 수정이 곧 살아있는 스펙

**▸ 판단·정책**
- **AI를 활용해 LNA 우회 20여 방안을 폭넓게 탐색**(HTTPS·WebSocket·Extension·WebRTC·mDNS·QUIC 등) → 장기 안정성 기준으로 Custom Protocol 채택
- WebSocket은 구현이 빠르나 "곧 LNA 적용 예정"이라 1~2년 후 재차단될 단기 해법 → 탈락. Custom Protocol은 HTTP 요청이 아니라 LNA 적용 대상이 아님
- 지속성: 이후 Solutions 모노레포로 통합. Electron+외부 SW의 E2E는 AI 컴퓨터 사용 기능으로 자동화 검토 중

`기술` Electron · Vite · React · TypeScript · Custom Protocol · Datadog

---

## 프로젝트 3. Electron 빌드·배포·자동 업데이트 인프라
`2024 초기 설계 → 2026.04 GitHub Actions 이관 → 진행 중` · Batch·Linker 공통 인프라 (팀 작업, 본인 최다 기여)

두 Electron 앱(Batch·Linker)의 빌드가 처음엔 **담당자 로컬 PC에서 수행해 업로드하는 구조**였습니다. 담당 PC가 멈추면 빌드가 멈추고, 빌드법을 아는 사람이 담당자뿐이라 요청이 몰렸으며, Windows는 코드사인 USB를 인프라팀이 관리해 매번 거쳐야 했습니다. 당시엔 풀지 못했고, 팀 개편 후 다시 맡으며 재설계했습니다.

**▸ 기여**
- Windows는 본인 PC를 **self-hosted 빌드머신**으로, Mac은 공증(notarization)에 필요한 자산을 사내 배포 저장소에 등록 → 코드사인 USB·인프라팀 의존 제거
- Azure 클라우드 러너의 느린 빌드를 **GitHub Actions self-hosted로 이관** *(20분대 → 6분 내외, 체감)* — 영구 캐시로 빌드 서버 부하를 회피하고 단계적으로 이관
- QA 빠른 설치 경로 설계: 빌드머신이 **사내 NAS에 직접 업로드** → QA가 자동 업데이트를 안 기다리고 최신 버전을 즉시 설치
- 코드 서명을 빌드 과정에 포함 — 서명을 빌드 뒤 따로 하면 자동 업데이트가 깨진다는 걸 개발 중 직접 부딪혀 학습한 결과

**▸ 판단·정책**
- 자동 업데이트는 electron-builder 채택 → 기본 배포 모듈이 사내 환경(호스트·인증)과 맞지 않아 **내부를 직접 분석해 패치**, 운영은 유지보수 부담을 고려해 설정 커스텀으로 단순화
- 지속성: blue-green 배포 중이나, green 검증 자동화는 데스크톱 앱 특성상 환경·빌드가 2배가 되는 **운영 비용 대비 가치**를 따져 의도적 보류

`기술` electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure → S3 · NAS · 코드 서명 · blue-green

---

## 프로젝트 4. 공통 모듈 통합 — Micro Frontend
`2024.11 ~ 2025.04 집중` · 초기 iframe 런타임 통합 주도 · 일부 서비스 Module Federation 담당 · 3D 뷰어 렌더링 정합(현재)

cloud·crown·modeler·milling이 도메인도 관리팀도 분리된 상황에서 공통 기능 4개(설정·내보내기·탐색기·뷰어)는 공유됐습니다. 이 기능이 하나 바뀔 때마다 **각 서비스에 같은 수정을 반복하고 전부 배포**해야 했고, 뷰어는 다른 팀이 관리하는 사내 3D 라이브러리에 묶여 있었습니다.

**▸ 기여**
- 라이브러리 배포 → 버전업·재배포 반복으로 문제 잔존 → **iframe + postMessage 런타임 통합** 주도 — cross-origin 격리와 호스트 검증으로 보안 확보
- 모노레포 통합 후 빌드타임 통합으로 옮겼다가, 다시 iframe 회귀하되 같은 origin으로 서빙해 CORS·인증 공유·origin 검증을 단순화
- 사내 3D 라이브러리 → Three.js 전환은 **팀 주도**(특정 라이브러리 종속 해소)이며, 본인은 **3D 뷰어의 렌더링 색상·표현 정합과 회귀 수정을 담당**(현재 진행)

**▸ 판단·정책**
- Module Federation은 다른 기능에 직접 도입해본 경험상 초기 설정·소비처 부담이 커 이 건에선 제외 — 몰라서가 아니라 써보고 뺀 판단이며, 별도 서비스엔 적용
- 빌드타임 통합 회귀 이유: 통합 배포가 실제론 잘 안 됐고 사내 배포 프로세스가 짧은 주기 배포를 어렵게 바뀜 → iframe의 한계(로딩 지연·중복 로딩·메시지 전송 제약)까지 직접 확인
- 지속성: "정답 기술 없이 조직·배포 제약에 맞춰 재선택"

`기술` NX · iframe + postMessage · Module Federation · Three.js · Webpack → Rspack

---

## 프로젝트 5. B2B SaaS 구독/계정 시스템 — Dentbird Account
`2023.09 ~ 2025.07` · 구독제 전환 FE 전담 설계·구현

회사가 일회성 크레딧 판매에서 반복 구독 매출로 전환하면서, 결제·권한 도메인의 프론트엔드를 글로벌 멀티테넌트·다국어 환경에서 전담했습니다. 동시에 계층형 구조의 한계 — 응집도가 낮고 결합도가 높아 변경 영향을 예측하기 어려운 — 를 마주했습니다.

**▸ 기여**
- 플랜 업그레이드·시트 구매·구독 취소/재개·쿠폰·결제수단·히스토리까지 **구독 워크플로우 전체 전담 구현** *(크레딧 레거시와 공존)*
- 명령형 방어 코드를 **ErrorBoundary 선언적 처리**로 이관하고, 결제 정보를 못 받아도 내역·구독취소·뒤로가기 같은 핵심 흐름은 살아남도록 Fault Tolerance 설계
- 서버가 HTTP 200으로 내려주는 **비즈니스 에러를 커스텀 에러로 코드별 분기**
- 도메인 단위로 API·데이터 변환 책임을 나누는 **FSD 레이어 규칙을 직접 설계**하고, 결제 도메인에 테스트 안전망을 도입

**▸ 판단·정책** (성장 서사)
- FSD·Compound Component·Render Props를 도입하며 **양면 트레이드오프까지 기록**(예: Render Props는 커스텀훅으로도 가능 — 과설계 경계)
- 한계 인식: FSD 폴더 위치 기준이 불명확해 **매번 상의하는 시간이 과다**했고, 패턴을 팀원이 다르게 이해해 사용법이 분기 → "패턴이 푸는 문제를 이해하지 못한 채 일관성을 명분으로 도입"
- **"기술 도입은 우선순위 높은 문제 해결이 먼저"** 원칙 확립. 구조 자체는 유효해 모노레포 이관 시 그대로 정착

`기술` React · TypeScript · TanStack Query · FSD · Compound Component·Render Props · ErrorBoundary · MSW · Jest → Vitest

---

## 프로젝트 6. 크라운 디자인 배치 처리 Electron 앱 — Dentbird Batch
`2023.12 ~ 2025.01` · 아키텍처 설계·구현 주도 (팀 개발, 최다 기여자)

치과 CAD 작업의 배치 처리를 위한 Electron 앱을 아키텍처부터 프로덕션 릴리스까지 주도했습니다.

- Main/Renderer 분리 IPC 구조 + 딥링크로 웹에서 앱 실행, Windows/Mac 크로스 플랫폼 지원
- HTTP 인터셉터로 토큰 자동 갱신·글로벌 에러 처리, 세션 만료 시 작업 창 자동 종료·재인증 처리
- VTK.js 기반 3D 뷰어의 마우스 인터랙션을 Win/Mac 통일, WASM 메모리 모니터링·크래시 리포팅으로 데스크톱 안정성 관측

> 빌드·코드 서명·자동 업데이트 인프라는 **프로젝트 3**으로 통합 정리.

`기술` Electron · React · TypeScript · Recoil · VTK.js · CRACO · Webpack · Datadog

---

## 프로젝트 7. 기업 랜딩 페이지 — Next.js 풀스택
`2023.09 ~ 2025.10` (입사 첫 업무, 이후 유지보수) · v3 전면 리뉴얼 주도 + Backoffice·서버 API

입사 첫 업무로 기업 랜딩 페이지 v3를 거의 단독 리뉴얼하고(2023-09~11 집중), 관리자 Backoffice와 서버 API까지 풀스택으로 개발했습니다.

- 번역 키 오타가 런타임에야 원본 키로 노출되던 문제를, **번역 리소스의 타입을 자동 생성해 컴파일 타임에 없는 키를 차단** *(다국어 확장이 안전한 구조)*
- 입사 3개월차에 조직 관리 백오피스의 **그룹·팀·멤버 3계층 CRUD를 FE부터 서버 API까지** 1주 만에 동시 풀스택으로 완성
- LCP·폰트 preload, 이미지 최적화, 정적 자원 캐시, SEO를 **Lighthouse로 측정하며 적용**

`기술` Next.js 13 · React 18 · TypeScript · i18next(타입세이프) · TanStack Query · Fastify · MongoDB

---

## 프로젝트 8. 사내 디자인 시스템 기여 — Imago Cloud Design System
`사내 디자인 시스템 공통 컴포넌트 기여`

"MUI를 커스텀하는 게 아니라 MUI가 제공하는 커스텀 시스템을 사용한다"는 원칙 아래 운영되는 사내 디자인 시스템에, 공통 컴포넌트로 기여했습니다.

- 날짜 선택 컴포넌트에 선택적 속성을 추가해 **하위 호환을 유지**하며 유연성을 확보
- 콜백 속성이 내부 로직에 덮어써지던 프로덕션 버그를 호출 순서 조정으로 해결
- v3 메이저 마이그레이션의 릴리스 관리 일부 담당

`기술` MUI · Emotion · Storybook · TypeScript

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
| **State / UI** | TanStack Query, Recoil · MUI, Emotion |
| **Build / Arch** | NX, pnpm, Module Federation, FSD, 런타임 Config |
| **Testing** | Playwright, Jest, Vitest, MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

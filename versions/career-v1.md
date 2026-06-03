# 경력기술서 — 김진완 (Jinwan Kim)

> **제품의 구조와 품질을 끝까지 책임지는 프론트엔드 개발자**
> React·TypeScript를 기반으로 화면 구현을 넘어 Electron 데스크톱, 빌드·배포 인프라, 테스트 자동화까지 — 제품이 안정적으로 돌아가는 구조를 만듭니다.

**Frontend Developer** | 이마고웍스 (ImagoWorks) · Dentbird 1 Team | 2023.09 ~ 현재

- [이력서](resume.html) · [포트폴리오](portfolio.html) · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 경력 요약

2023.09부터 AI 기반 치과 CAD/CAM SaaS(DentBird)에서 화면 구현을 넘어 **제품의 구조와 품질을 책임지는** 프론트엔드 개발자로 일해왔습니다. 분산된 앱을 NX 모노레포로 통합하고 공통 모듈을 Micro Frontend로 분리하는 플랫폼 구조를 설계했고, 변경된 코드를 분석해 관련 E2E만 자동 실행하는 시스템과 커밋 단위 격리 재현 환경으로 릴리스 신뢰성을 끌어올렸습니다. Electron 데스크톱 앱을 0→1로 설계하고 빌드를 20분대에서 6분 내외로 줄이는 등, FE를 중심으로 제품의 처음부터 끝까지 다룹니다.

- **범위** — FE를 중심으로 Electron 데스크톱과 빌드·배포 인프라, 일부 백엔드까지 제품 전반을 다룹니다.
- **구조** — 분산된 앱을 모노레포로 통합하고, 공통 모듈을 MFE로, 환경 설정을 런타임으로 분리하는 플랫폼 구조를 설계합니다.
- **판단** — 기술 결정을 여러 대안과 비교하고, 라이브러리 내부까지 파고들어 근거를 남기고 팀과 공유합니다.
- **효율** — 격리 재현 환경과 E2E·AI 자동 테스트, 빌드 파이프라인 재설계로 디버깅과 릴리스의 신뢰성을 높입니다.

---

## 핵심 역량

- **플랫폼 아키텍처**: NX 모노레포 통합, Micro Frontend(iframe·Module Federation), 런타임 환경 분리
- **품질 자동화**: Playwright E2E, 커밋 단위 Docker 격리 재현, AI 기반 변경 감지 테스트
- **데스크톱**: Electron 앱 0→1 설계·프로덕션 릴리스, 빌드·자동 업데이트·코드 서명 인프라
- **제품 도메인**: B2B SaaS 구독/결제 FE 전담, 글로벌 멀티테넌트·다국어

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(DentBird)의 프론트엔드 전반을 설계·구축했습니다. 모노레포 통합, Micro Frontend 전환, 런타임 인프라, 품질 자동화 등 플랫폼 기술 결정을 주도했습니다.

---

## 프로젝트 1. DentBird Solutions — 플랫폼 인프라 & 품질 자동화

*효율 · 구조*

| 항목 | 내용 |
|------|------|
| **기간** | 2024.06 ~ 현재 |
| **역할** | 런타임 환경 분리 핵심 설계 참여 · 격리 재현/E2E 자동화 주도 |
| **기술** | React, TypeScript, Playwright, Docker, NX Monorepo, AWS EC2, Claude Code CLI, Datadog |

### 배경

레거시 모노레포 통합 작업 중, dev·qa에서는 멀쩡하던 것이 prod 배포 시 엉뚱한 환경으로 요청을 보내거나 환경변수 파일이 누락되어 **배포 직후 핫픽스를 다시 배포하는 일**이 반복됐습니다. 동시에 특정 시점의 버그를 재현하기 어려웠고, E2E가 환경 차이로 쉽게 깨져 신뢰하기 어려웠습니다.

### 기여

- 원인이 환경변수를 **빌드타임에 박아 넣는 구조**임을 진단하고, 환경변수를 **런타임에 주입**하는 구조로 전환하는 설계에 핵심적으로 참여했습니다. 같은 빌드 결과물 하나를 dev·qa·prod에 그대로 올리고 환경변수만 교체하도록 만들어, 환경별 빌드 시간을 줄이고 배포 직후 핫픽스 반복을 해소했습니다.
- 런타임 분리가 연 가능성을 활용해, **특정 커밋 시점의 클라이언트+서버를 Docker로 재현**하는 격리 환경을 주도 구축했습니다. qa·prod의 특정 데이터를 격리 환경에 복제해 그 시점을 그대로 재현함으로써, 환경 차이로 깨지던 디버깅·E2E를 안정적인 기반 위로 옮겼습니다.
- EC2에 Claude를 띄워 커밋 변경을 분석하고, 전체 E2E를 매번 돌리는 대신 **변경과 관련된 테스트만 자동 선별·실행**해 Teams로 보고하는 AI 변경 감지 시스템을 구축했습니다. 검증을 10분 주기로 자동화해 회귀를 같은 PR 안에서 조기에 잡도록 했습니다.
- Playwright 기반 E2E 프레임워크를 주도 구축하며, 테스트마다 반복되던 로그인 셋업 코드(약 60줄)를 공통 Page Object 호출로 대체해 **중복을 93% 제거**하고, 로그인 세션 공유로 전체 스위트 실행 시간을 **18분 → 11분(39%↓)** 단축했습니다.
- 레거시 모노레포 통합 시 **TypeScript 에러 688 → 0**으로 정리하고, Facade 패턴으로 토큰 관리 코드를 **200 → 33줄(85%↓)** 로 줄이며 Race Condition·Stale Closure를 해결하고 단위 테스트 22개로 가드했습니다.

### 트레이드오프 · 지속성

격리 환경을 처음부터 만든 것이 아니라, 런타임 config·앱 통합으로 **"격리하기 쉬운 환경"을 먼저 깔고 그 위에 쌓는** 순서를 택했습니다. 디버깅·테스트 신뢰성 문제를 "테스트를 더 짜는" 대신 **재현 가능한 환경을 만드는 방향**으로 풀었고, 이 인프라가 이후 E2E·AI 변경 감지 자동화의 실행 기반으로 확장돼 지금도 운영되고 있습니다.

---

## 프로젝트 2. DentBird Linker — CAM 연동 Electron 데스크톱 앱

*판단 · 효율 · 범위*

| 항목 | 내용 |
|------|------|
| **기간** | 2024.07 ~ 2025.12 (이후 Solutions로 통합) |
| **역할** | 아키텍처 설계·구현 주도 (주 개발자) |
| **기술** | Electron, Vite, React, TypeScript, Custom Protocol, draco3d, Datadog |

### 배경

클라우드에서 설계한 결과물을 사용자 PC의 CAM 소프트웨어로 보내야 했지만, Chrome의 **Local Network Access(LNA)** 정책이 웹→로컬 통신을 차단했습니다. 권한 팝업을 놓친 사용자의 CS 문의가 다수 발생했고, "지금 되는 방법"을 택하면 정책이 강화될 때 다시 막히는 구조였습니다.

### 기여

- LNA를 우회할 방안으로 HTTPS 로컬 서버·WebSocket·Chrome Extension·WebRTC·mDNS 등을 비교한 뒤, **구현 속도가 아니라 장기 안정성을 기준으로 Custom Protocol을 채택**했습니다. WebSocket은 구현이 빠르나 향후 LNA 적용 대상이라 다시 막힐 단기 해법으로 판단해 탈락시켰고, Custom Protocol은 HTTP 요청이 아니어서 LNA 영향을 받지 않는 영구적 우회로 선택했습니다.
- Electron + Vite + React 아키텍처를 설계해 한 달여 만에 v1.0.3까지 프로덕션 릴리스하고, `dentbird-linker://` 딥링크 통신(앱 미실행 시 URL 저장 후 처리)과 draco3d 기반 **DRC → STL 실시간 변환 파이프라인**을 구축했습니다.
- **12개 CAM 소프트웨어**별 좌표계 차이를 `transformMat` 변환 매트릭스 알고리즘으로 흡수하고, CAM별 정합 회귀 테스트로 검증해 어떤 CAM으로 내보내도 좌표 오류 없이 동작하도록 만들었습니다.
- INI/XML 생성·HTTP 통신 로직을 TDD로 구현해 단위 테스트 18개(INI 5 · HTTP 8 · config 5)로 가드하고, Datadog RUM/Logs로 Main/Renderer 프로세스 에러 추적 체계를 구축했습니다.

### 트레이드오프 · 지속성

당장 빠른 WebSocket 대신 Custom Protocol을 택한 결정은 "단기 구현 속도 vs 장기 안정성"의 trade-off를 장기 쪽으로 끊은 판단이었습니다. 이후 분산돼 있던 Electron 앱을 NX 모노레포(Solutions)로 통합하며 동일 구조 위에서 운영되고 있습니다.

---

## 프로젝트 3. Electron 데스크톱 빌드·배포·자동 업데이트 인프라

*효율 · 판단*

| 항목 | 내용 |
|------|------|
| **기간** | 초기 Batch 배포 설계(2024) → GitHub Actions 이관 주도(2026.04) → 진행 중 |
| **역할** | 빌드·배포·자동 업데이트 파이프라인 설계·이관 주도 (Batch·Linker 공통) |
| **기술** | electron-builder, electron-updater, GitHub Actions(self-hosted), Azure→S3, NAS, notarytool, signtool, blue-green |

### 배경

두 Electron 앱(Batch·Linker)의 빌드는 처음에 **담당자 로컬 PC에서 직접 수행해 업로드하는 구조**였습니다. 담당 PC를 못 쓰면 빌드가 멈추고, 빌드 방법을 아는 사람이 담당자뿐이라 요청이 한 사람에게 몰렸으며, Windows는 코드사인 USB를 인프라팀이 관리해 매번 인프라팀을 거쳐야 했습니다. 당시에는 이 문제를 풀지 못했고, 팀 개편 후 다시 맡으며 본격적으로 재설계했습니다.

### 기여

- Mac은 notarize에 필요한 파일을 DevOps Library에 등록해 자동화하고, Windows는 **본인 PC를 self-hosted 빌드머신**으로 구성해 코드사인 USB·인프라팀 의존을 끊었습니다.
- Azure Pipelines가 클라우드 러너라 빌드가 20분 넘게 걸리는 문제를, **GitHub Actions + self-hosted 러너로 이관 주도**해 해결했습니다(#7689). 영구 캐시 경로로 BrokerServer 부하를 회피하고 Phase 단위로 단계적 이관 후 macOS universal·버전 전파 회귀 등을 안정화했습니다. 그 결과 빌드 시간을 **20분대 → 6분 내외(직접 비교)** 로 줄이고 담당자·인프라팀 의존을 제거했습니다.
- 자동 업데이트는 커스터마이징 용이성을 기준으로 electron-builder를 채택하고, 기본 Publisher가 환경에 맞지 않자 **`BitbucketPublisher` 클래스를 직접 분석해 hostname 버그·timeout·Bearer 인증을 패치**했습니다(issue #6192 기반). 운영에는 유지보수 부담을 고려해 클래스 교체 대신 yaml만 커스텀하는 방향으로 단순화했습니다.
- QA/PROD 채널을 `latest.yml`로 분리해 사용자가 QA 빌드를 잘못 수신하는 사고를 차단했고, **코드 서명 없이 빌드한 아티팩트를 나중에 서명하면 checksum이 바뀌어 자동 업데이트가 깨진다**는 점을 개발 중 직접 부딪혀, 코드 서명을 빌드 과정에 포함하는 구조로 정리했습니다.

### 트레이드오프 · 지속성

현재 blue-green으로 배포하되, S3 업로드 순간 자동 업데이트가 즉시 발동돼 검증 전 배포 위험이 있어 green 검증은 플래그 명령어로 수행합니다. green 검증 자동화는 가능하지만, 데스크톱 앱 특성상 환경이 qa/prod별로 두 배가 되고 빌드를 한 번 더 해야 하는 **운영 비용 대비 가치**를 따져 의도적으로 보류한 상태입니다. (Batch는 팀 개발이며 본인이 최다 기여자로 인프라·빌드를 전담했습니다.)

---

## 프로젝트 4. Micro Frontend — 공통 모듈 통합

*구조 · 판단*

| 항목 | 내용 |
|------|------|
| **기간** | 2024.11 ~ 2025.04 집중 (이후 이관·회귀로 이어짐) |
| **역할** | 초기 iframe 런타임 통합 주도 · console-client Module Federation 담당 |
| **기술** | NX, iframe + postMessage, COEP/CORP/COOP, Module Federation(@module-federation/enhanced), Webpack→Rspack |

### 배경

cloud·crown·modeler·milling이 도메인도 관리팀도 분리된 상황에서, 공통 기능 4개(setting·export·explorer·viewer)는 공유되고 있었습니다. 이 기능이 하나 바뀔 때마다 **각 서비스에 같은 수정을 반복하고 전부 배포**해야 하는 비용이 문제였습니다.

### 기여

- 먼저 컴포넌트를 **라이브러리로 배포**해봤지만 각 서비스가 버전을 올리고 다시 배포해야 해서 풀려던 문제가 그대로 남았습니다. 그래서 런타임 통합으로 방향을 틀어, 가장 빠르고 단순한 **iframe + postMessage(typed, same-origin)** 통합을 주도했습니다.
- **Module Federation**은 notification 기능에서 직접 도입해본 경험상 초기 설정이 복잡하고 모노레포가 아닌 소비처에서 설정 부담이 커서 이 건에선 제외했습니다 — 몰라서가 아니라 써보고 트레이드오프로 뺀 판단이며, 별도로 console-client에는 Module Federation을 직접 적용했습니다.
- 4개 앱이 한 모노레포로 합쳐진 뒤에는 빌드 포함이 쉬워져 **빌드타임 통합**으로 옮겼습니다.

### 트레이드오프 · 지속성 ★

빌드타임 통합 후 운영해보니 **통합 배포가 실제로는 잘 이뤄지지 않았고**, 사내 배포 프로세스가 짧은 주기 배포를 어렵게 바뀌면서 배포 부담이 커졌습니다. 그래서 **다시 iframe 런타임 통합으로 회귀**하되, 이번엔 모듈별 도메인 대신 cloud 도메인 하위 same-origin으로 서빙해 CORS·인증 공유를 단순화했습니다. 이 과정에서 iframe의 비용(로딩 지연, 라이브러리 중복 로딩, postMessage의 File/Blob 제약)까지 직접 확인하며, **"통합 전략에 정답 기술은 없고 조직·배포 제약에 맞는 선택이 있을 뿐"** 임을 체득했습니다. (이후 공통 모듈의 vendor lock-in을 줄이기 위한 iwtk→Three.js 전환은 팀 차원으로 진행됐으며, 본인은 설계 참여 및 Viewer 렌더링 수정을 맡았습니다.)

---

## 프로젝트 5. DentBird Account — B2B SaaS 구독/계정 시스템

*범위 · 제품*

| 항목 | 내용 |
|------|------|
| **기간** | 2023.09 ~ 2025.07 |
| **역할** | 구독제 전환 FE 전담 설계·구현 |
| **기술** | React, TypeScript, TanStack Query, FSD, Compound Component·Render Props, ErrorBoundary, MSW, Jest→Vitest |

### 배경

회사가 일회성 크레딧 판매에서 반복 구독 매출로 비즈니스 모델을 전환하면서, 결제·권한 도메인의 프론트엔드를 글로벌 멀티테넌트·다국어 환경에서 전담했습니다. 동시에 클라이언트 코드가 구조 기준 없이 커지며 응집도는 낮고 결합도는 높아, 요구사항이 바뀌면 어디를 고쳐야 하고 어디가 깨질지 장담하기 어려웠습니다.

### 기여

- 플랜 업그레이드, 시트 구매, 구독 취소/재개, 쿠폰, 결제수단, 결제 히스토리까지 **구독 워크플로우 전체를 전담 구현**했습니다. 일할 계산·구독 취소/재개처럼 금액이 어긋나기 쉬운 흐름에서 발생하던 금액·상태 불일치를 클라이언트 검증과 단위 테스트로 막아 결제 정확성을 확보했습니다.
- 한 기능의 오류가 전체 페이지를 멈추던 구조를 **ErrorBoundary 기반 선언적 에러 처리로 기능 단위로 격리**하고, 결제 정보를 못 받아도 결제내역·구독취소·뒤로가기는 살아남도록 도메인 의존 관계 기준으로 Fault Tolerance를 구분했습니다. 서버의 `success:false` 응답은 커스텀 BusinessLogicError로 throw해 에러 코드별로 분기했습니다.
- FSD로 폴더 역할과 도메인 데이터를 분리하고, 구독·시트·빌링 도메인에 테스트 32개를 붙여 본인의 첫 테스트 코드 자산을 만들었습니다.

### 트레이드오프 · 성장 서사 ★

FSD·디자인 패턴을 적극 도입해봤지만, 파일을 어디에 둘지 매번 상의하는 비용이 들었고 **굳이 적용하지 않아도 될 곳에 "일관성"을 명분으로 패턴을 넣은 경우**가 많았습니다. 돌아보니 디자인 패턴은 오래 누적된 공통 문제의 해법인데 그것이 푸는 문제를 정확히 이해하지 못한 채 도입했던 것이었습니다. 여기서 **"기술 도입은 우선순위 높은 문제 해결이 먼저"** 라는 원칙을 얻었습니다. 다만 구조 자체가 나빴던 것은 아니어서, 이 코드들은 이후 모노레포로 이관되며 FSD 전면 적용·ErrorBoundary·BusinessLogicError가 그대로 정착했습니다.

---

## 프로젝트 6. DentBird Batch — 크라운 디자인 배치 처리 Electron 앱

*범위*

| 항목 | 내용 |
|------|------|
| **기간** | 2023.12 ~ 2025.01 |
| **역할** | 아키텍처 설계·구현 주도 (팀 개발, 최다 기여자) |
| **기술** | Electron, React, TypeScript, Recoil, VTK.js, Webpack, CRACO, Datadog |

### 배경 · 기여

치과 CAD 크라운 디자인 작업의 배치 처리를 위해 Electron 앱을 설계하고, 아키텍처부터 v1.0.13 프로덕션 릴리스까지 개발을 주도했습니다(팀 내 최다 기여자).

- Main/Renderer 프로세스를 분리한 IPC 통신 구조와 `dentbird://` 딥링크로 웹에서 앱을 실행하는 구조를 설계하고, Windows/Mac 크로스 플랫폼을 지원했습니다.
- Axios 인터셉터 기반 토큰 자동 갱신과 글로벌 에러 처리(`processOnLocal`/`processOnServer`)를 구축하고, 세션 만료 시 Crown Window 자동 종료·재로그인 토큰 클리어를 처리해 인증 안정성을 높였습니다.
- VTK.js 기반 3D Viewer의 마우스 인터랙션을 Windows/Mac에서 통일하고, WASM 메모리 모니터링·Crash Reporter·Datadog RUM으로 데스크톱 환경의 안정성을 관측 가능하게 만들었습니다.

> 빌드·코드 서명·자동 업데이트 인프라는 **프로젝트 3**으로 통합 정리했습니다.

---

## 프로젝트 7. 기업 랜딩 페이지 (Next.js 풀스택)

*범위*

| 항목 | 내용 |
|------|------|
| **기간** | 2023.09 ~ 2025.10 (입사 첫 업무, 이후 유지보수) |
| **역할** | v3 전면 리뉴얼 주도 + Backoffice·서버 API |
| **기술** | Next.js 13, React 18, TypeScript, i18next(타입세이프), TanStack Query, Fastify, MongoDB |

### 배경 · 기여

입사 첫 업무로 회사 프로세스를 익히며 기업 랜딩 페이지 v3를 거의 단독으로 리뉴얼하고, 관리자 Backoffice와 서버 API까지 풀스택으로 개발했습니다.

- 번역 키 오타가 **UI에 raw 키로 노출되어 런타임에야 발견되던 문제**를, `i18next-resources-for-ts`로 번역 리소스의 타입을 자동 생성해 **컴파일 타임에 없는 키를 차단**하는 구조로 해결했습니다. 이 구조가 이후 11개 언어 확장의 토대가 됐습니다.
- 입사 3개월차에 회사 소개 페이지의 조직 관리 백오피스를 맡아, **Groups → Teams → Members 3계층 CRUD를 프론트엔드부터 서버 PATCH API까지** 직접 구현하며 FE·서버를 동시에 다룬 첫 경험을 쌓았습니다.
- LCP·폰트 preload, 이미지 고해상도·quality 대응, 정적 자원 캐시 등 성능 기법을 적용했습니다.

---

## 프로젝트 8. Imago Cloud Design System 기여

*범위*

사내 디자인 시스템(`@imago-cloud/design-system`)에 기여했습니다. React 19 마이그레이션으로 Storybook 7 호환성과 의존성 충돌을 해결해 v3 브랜치 기반을 마련했고, DatePicker 컴포넌트에 placeholder·onClick props를 Optional 설계로 추가해 **하위 호환성을 유지**하며 유연성을 높였습니다. onClose props가 내부 로직에 덮어써지던 프로덕션 버그는 호출 순서를 조정해 당일 긴급 배포로 대응했습니다.

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

## 정량 성과 (실측·직접 비교 기준)

| 영역 | 지표 | 수치 |
|------|------|------|
| 빌드 인프라 | Electron 빌드 시간 (Azure 러너 대비, 직접 비교) | 20분대 → 6분 내외 |
| 코드 품질 | 모노레포 통합 TypeScript 에러 | 688개 → 0개 |
| | 토큰 관리 코드 (Facade) | 200줄 → 33줄 (85%↓) |
| 테스트 | E2E 로그인 셋업 중복 제거 | 약 60줄 → 공통 호출 (93%↓) |
| | E2E 스위트 실행 시간 (세션 공유) | 18분 → 11분 (39%↓) |
| | 단위 테스트 | 토큰 관리 22개 · Custom Protocol 18개 |
| 제품 | CAM 소프트웨어 연동 | 12종 |
| | Electron 릴리스 | Batch ~v1.0.13 · Linker ~v1.0.3 |

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

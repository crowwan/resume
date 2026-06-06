# 김진완 (Jinwan Kim) | Frontend Developer

> **웹 기술로 하드웨어 위 소프트웨어를 만들어 온 프론트엔드 개발자**
> 웹↔로컬 디바이스 경계·Electron 빌드/배포·웹뷰 번들 결의 모노레포를 직접 설계·운영하고, 자기 결정의 후과(부채·자기폐기)까지 추적합니다.

- [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 토스플레이스에

토스플레이스가 푸는 문제 — 웹 기술 단일 코드베이스로 POS·키오스크를 전 OS에 올리고, 웹뷰는 빠르게 바꾸되 시리얼 프린터·결제 단말 같은 하드웨어는 네이티브가 안정적으로 책임지는 구조 — 는 제가 지난 2년간 매일 풀던 문제와 같은 계열입니다.

저는 웹에서 만든 데이터를 사용자의 로컬 CAM 장비로 흘려보내는 Electron 앱을, 브라우저가 로컬 통신을 막는 정책(Chrome LNA) 위에서 단독 설계·재설계·운영했습니다. 좌표계도 전달 방식도 제각각인 외부 장비 12종을 단일 인터페이스로 통합하고, 담당자 PC에 묶여 있던 빌드를 self-hosted 빌드머신·서명 에이전트 풀로 끌어올렸습니다. 토스플레이스가 "버튼 하나 추가에도 전체 앱 빌드" 문제를 웹뷰 번들로 풀어낸 그 진화 경로를, 저도 빌드 한계를 직접 겪고 재설계하며 같은 방향으로 걸어왔습니다.

복잡한 하드웨어 연동을 단일 인터페이스로 추상화하고, 제가 깐 토대도 수명이 다하면 직접 거두는 — 끝까지 책임지는 DRI에 맞는 사람이라고 생각합니다.

---

## 핵심 역량

- **웹↔하드웨어 경계 설계** — 웹↔로컬 디바이스 브리지(Chrome LNA 대응 Custom Protocol), 외부 장비 12종 단일 인터페이스 통합, 부모↔팝업 origin 격리
- **Electron 데스크톱** — 프로덕션 릴리스, self-hosted 빌드머신·서명 에이전트 풀 단독 운영, 코드 서명·자동 업데이트·blue-green
- **플랫폼 아키텍처** — Micro Frontend(iframe·Module Federation), NX 모노레포 이관(Git Subtree), 도메인 통합 라이브러리
- **품질 자동화** — Playwright E2E, 커밋 단위 컨테이너 격리 재현, AI 기반 변경 감지, 자작 픽셀 metric visual regression
- **결제·B2B SaaS** — 구독·결제 FE 전담, 부분 실패에도 무너지지 않는 정확성 검증 구조, 글로벌 멀티테넌트

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(Dentbird)의 프론트엔드를 설계·구축했습니다. 웹↔로컬 디바이스 연동(Linker)과 Electron 빌드·배포 인프라를 단독 운영하고, MFE·모노레포 토대, 3D 렌더 품질 자동화, 결제 도메인까지 폭넓은 기술 결정을 맡았습니다.

---

## 프로젝트

### Dentbird Linker — 웹↔로컬 CAM 디바이스 브리지
`2024.07 ~ 2025.12 (이후 Solutions 통합, 2026.06까지 단독 운영)` · 설계·재설계·단독 운영

웹에서 생성한 보철 케이스를 사용자의 로컬 CAM 소프트웨어로 전달하는 데스크톱 앱. 자체 로컬 서버가 없는 장비까지 연동해야 하고, 브라우저→로컬 통신을 막는 Chrome LNA 정책 위에서 안정적인 브리지를 세워야 했습니다.

- 초기 로컬 서버 방식이 Chrome LNA 정책에 막혀, 20여 방안 비교 끝에 Custom Protocol 중간 레이어로 재설계
- WebSocket은 곧 같은 정책에 막힐 단기 해법이라 제외하고 장기 안정성을 우선
- 초기 핵심이던 로컬 서버를 직접 폐기 — 스스로 도입한 토대도 수명이 다하면 직접 거두는 판단
- 외부 CAM 12종의 제각각인 좌표계·전달 방식을 단일 변환·연동 인터페이스로 통합 — 복잡한 하드웨어 차이를 호출자에게는 단일 추상으로
- Datadog 연동으로 데스크톱 앱 운영 관측성 확보, 딥링크·세션·장비 연동 이슈를 2026년까지 단독 책임
- 앱 실행 감지는 포커스 변화 휴리스틱 의존이 한계로, 핵심 경로에서 분리하는 방향으로 개선 중

`기술` Electron · Vite · React · TypeScript · Custom Protocol · 특성화 테스트 · Datadog

### Electron 빌드·배포·자동 업데이트 인프라 — 담당자 PC 의존을 단독 운영 파이프라인으로
`2024 초기 → 2026 GitHub Actions 이관 · Batch·Linker 공통(본인 최다 기여·단독 운영)`

두 데스크톱 앱의 빌드가 담당자 로컬 PC에서 돌려 올리는 구조라, 그 PC가 멈추면 빌드가 멈추고 Windows 서명 USB는 인프라팀을 매번 거쳐야 했던 출시 파이프라인을 재설계했습니다.

- macOS 빌드 파이프라인 재설계로 전체 33~39분을 17~24분으로 56% 단축 — artifact 757→334MB·업로드 114→47초 동반 축소(PR 실측)
- Windows 코드 서명을 물리 빌드머신의 전용 서명 에이전트 풀로 직접 설치·운영 — CI 설정을 넘어 빌드 인프라 자체를 단독 운영하며 서명 USB·인프라팀 의존 제거
- 서명을 빌드 과정 안에 통합 — 빌드 뒤 따로 서명하면 바이너리가 바뀌어 자동 업데이트가 깨지는 문제를 직접 겪은 결과
- macOS 아키텍처를 Universal→arm64→Intel로 11일 만에 3연속 번복 — 용량 2배 트레이드오프를 도입·운영·회수까지 직접 감당한 판단
- blue-green은 도입했으나 green 검증 자동화는 환경·빌드가 2배가 되는 운영 비용 대비 가치를 따져 의도적 보류

`기술` electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3 · NAS · blue-green

### 공통 모듈 통합(MFE) + NX 모노레포 이관
`MFE 2024.11 ~ 2025.04 집중 · 모노레포 이관 2024.06 ~ 현재` · MFE 런타임 통합 주도 / 모노레포 이관 owner

도메인도 관리팀도 나뉜 여러 서비스가 공통 기능 4종(설정·내보내기·탐색기·뷰어)을 공유해, 하나가 바뀔 때마다 각 서비스에 같은 수정을 반복하고 전부 배포해야 했습니다.

- 공통 기능 변경을 각 서비스 반복 수정에서 모듈 한 곳 수정으로 좁힌 iframe + postMessage 런타임 통합 주도 — cross-origin 격리 헤더·호스트 화이트리스트로 임베드 경계 잠금
- 별도 레포의 앱 2종·공용 라이브러리 6종을 메인 NX 모노레포로 이관 — Git Subtree Split으로 커밋 이력 보존·네임스페이스 리네임·정기 동기화 운영
- 빌드타임 통합으로 옮겼다가 통합 배포가 거의 안 일어나고 사내 배포 주기가 길어, 같은 origin iframe으로 회귀 — 정답 기술이 아니라 조직·배포 제약에 맞춘 재선택
- Module Federation은 다른 기능에 직접 도입해 초기·소비처 설정 부담을 확인한 뒤 이 건엔 제외 — 몰라서가 아니라 트레이드오프로 가른 제외

`기술` NX · iframe + postMessage · Module Federation · Git Subtree · Next.js

### Dentbird Account — B2B SaaS 구독·결제 FE
`2023.09 ~ 2025.07` · 구독제 전환 FE 전담 설계·구현

일회성 크레딧 결제에서 반복 구독 매출로 비즈니스 모델을 전환하며, 글로벌 멀티테넌트 환경의 결제·권한 도메인 FE를 전담했습니다. 결제는 정확성이 곧 신뢰라, 기능 구현을 넘어 부분 실패에도 무너지지 않는 검증 구조까지 함께 설계했습니다.

- 플랜 업그레이드·시트 구매·구독 취소/재개·쿠폰·결제수단·히스토리까지 구독 워크플로우 전체를 레거시 크레딧 결제와 공존시키며 전담 구현
- HTTP 200으로 내려오는 서버의 비즈니스 에러를 커스텀 에러로 받아 코드별 분기 — 결제 정보를 못 받아도 내역 조회·구독 취소·뒤로가기는 살아남도록 Fault Tolerance를 도메인 의존 관계 기준으로 설계
- 결제 상태 동기화의 무한 폴링을 폴링 안전장치로 교정 — 기본 3초 간격·최대 20회 후 종료·언마운트 시 정리
- 구독을 만든 사람이 운영자 측 구독 회귀 가드까지 작성 — 구현부터 품질까지 한 사람이 지는 종단 책임

`기술` React · TypeScript · react-query · Stripe · FSD · ErrorBoundary · Playwright(E2E)

### 품질 자동화 토대 — 커밋 단위 격리 재현 · E2E 통합 · AI 변경 감지
`2025.11 ~ 현재` · 격리 daily 진단·근본 수정 / AI 변경 감지 구축 전담

다른 변경의 간섭으로 흔들리던 공유 환경 E2E를, 특정 커밋 시점을 결정론적으로 재현하는 격리 환경 위에 올리고 만성 실패를 근본 수정했습니다.

- 커밋 시점의 클라이언트·서버·DB를 컨테이너로 묶어, 다른 변경 간섭 없이 결정론적으로 재현 — 공유 환경 의존 제거
- 만성 실패하던 격리 daily를 네 개 독립 원인으로 분리 진단 후 두 건 근본 수정 — 결제 테스트 누락 약 28건을 0으로 정합화(통과 5→14)
- 실행 인프라를 K8s에서 EC2로 재전환해 선택 재실행·디버그·즉시 반영을 얻고 상시 클러스터 유지비 제거 — 운영비 월 약 $150에서 $30
- AI 변경 감지: 커밋 diff에서 변경 파일을 분석해 QA팀 qase 테스트 케이스 중 연관 케이스만 자동 선별·실행 → Teams 보고 + 실제 회귀/테스트 코드 문제 1차 분류 (현재 로컬 주기 실행으로 재편 중)

`기술` Playwright · Docker · MongoDB · EC2 · K8s · GitHub Actions(self-hosted) · Claude Code CLI · Teams Webhook

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
| **Build / Arch** | NX, pnpm, Git Subtree, Module Federation, FSD, 런타임 Config |
| **Testing** | Playwright, Vitest, pngjs(visual regression), MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs, ErrorBoundary |
| **3D / Render** | Three.js, draco3d, WebGL, SRGB ColorManagement |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08
</content>
</invoke>

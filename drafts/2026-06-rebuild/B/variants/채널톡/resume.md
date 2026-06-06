# 김진완 (Jinwan Kim) | Frontend Developer

> **"이건 왜 이렇게 되어 있지?"에 끝까지 답하는 프론트엔드 개발자**
> 모노레포 토대·웹↔로컬 데스크톱 경계·품질 자동화를 직접 설계·운영하고, 자기 결정의 후과(부채·자기폐기)까지 추적합니다.

- [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

채널톡이 모노레포 도구로 Nx 대신 yarn workspace와 TS Project References를 택한 글을 읽고, 도구가 아니라 그 판단의 결이 닿았습니다. 저는 NX를 상속받은 쪽이었고, 전환 이득이 이관·재학습 비용을 넘지 못한다고 보아 그대로 유지했습니다 — 새로 고른 게 아니라, 안 바꾸기로 결정한 쪽입니다. "블록은 신중하게, 시스템은 빠르게"라는 말에도 같은 감각을 느낍니다. 직접 도입한 토대를 수명이 다하면 스스로 폐기하는, 결정의 끝까지 책임지는 일을 해왔습니다.

---

## 핵심 역량

- **플랫폼 아키텍처** — 도메인 통합 라이브러리 토대, NX 모노레포 이관(Git Subtree), 모노레포 도구 유지/전환 비용-편익 판단
- **데스크톱** — Electron 프로덕션 릴리스(Win/macOS), self-hosted 빌드머신·코드 서명 에이전트 풀 단독 운영, 웹↔로컬 디바이스 브리지
- **품질 자동화·DX** — 커밋 단위 컨테이너 격리 재현, Playwright E2E 통합, AI 기반 변경 감지, 자작 픽셀 metric visual regression
- **경계 설계** — 웹↔로컬(Chrome LNA 대응), 부모↔팝업 origin 격리, 두 3D 렌더 엔진 책임 경계
- **관측성** — 3-layer ErrorBoundary, Datadog RUM 표준 정렬

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(Dentbird)의 프론트엔드를 설계·구축. 도메인 통합 라이브러리로 플랫폼 토대를 세우고, 모노레포 이관·웹↔로컬 디바이스 연동·품질 자동화·Electron 빌드 인프라·관측 표준 정렬까지 폭넓은 기술 결정을 맡았습니다.

---

## 프로젝트

### DentBird Solutions 플랫폼 토대 — 도메인 통합 라이브러리 · 모노레포 이관
`2024.06 ~ 현재 · 도메인 통합 라이브러리 단독 / 모노레포 이관 owner`

여러 클라이언트 앱이 환경별 URL·도메인을 제각기 구성하던 구조라, 도메인 파생을 한 곳으로 모으는 통합 라이브러리부터 세워 그 위에 플랫폼 인프라가 쌓이도록 토대를 잡았습니다.

- 환경별로 흩어진 도메인·URL 구성을 단일 라이브러리로 일원화 — 팀의 런타임 환경 분리·격리 재현 환경이 이 위에 얹히는 시작점
- 별도 레포의 앱 2종·공용 라이브러리 6종을 메인 NX 모노레포로 Git Subtree Split 이관 — 커밋 이력 보존·네임스페이스 충돌 리네임·정기 동기화 운영
- NX 자동 타겟 추론이 일으키던 빌드 오류를 진단, 불필요한 산출물 설정 제거로 빌드 태스크 11개→9개 축소·메모리 부족 해소
- 모노레포 도구를 새로 고르지 않고 기존 NX 유지 — 전환 이득이 이관·재학습 비용을 넘지 못한다고 본 비용-편익 판단

`기술` TypeScript · NX 모노레포 · Git Subtree · 도메인 통합 라이브러리

---

### Dentbird Linker — 웹↔로컬 CAM 디바이스 브리지
`2024.07 ~ 2025.12 (이후 Solutions 통합, 2026.06까지 단독 운영) · 설계·재설계·단독 운영`

웹에서 생성한 보철 케이스를 사용자의 로컬 CAM 소프트웨어로 전달하는 데스크톱 앱. 기존엔 다운로드·압축 해제·수동 투입을 거쳐야 했고, 자체 로컬 서버가 없는 CAM은 연동 자체가 불가능했습니다.

- 초기 로컬 서버 방식이 Chrome LNA 정책에 막혀, 20여 방안 비교 끝에 Custom Protocol 중간 레이어로 재설계
- WebSocket은 곧 같은 정책에 막힐 단기 해법이라 제외하고 장기 안정성을 채택 기준으로
- 초기 핵심이던 로컬 서버를 직접 폐기 — 스스로 도입한 토대도 수명이 다하면 직접 거두는 판단
- 외부 CAM 12종의 제각각인 좌표계·전달 방식을 단일 변환·연동 인터페이스로 통합
- 앱 실행 감지는 포커스 변화 휴리스틱 의존이 한계로, 핵심 경로에서 분리하는 방향으로 개선 중

`기술` Electron · Vite · React · TypeScript · Custom Protocol · 특성화 테스트 · Datadog

---

### 품질 자동화 토대 — 커밋 단위 격리 재현 · E2E 통합 · AI 변경 감지
`2025.11 ~ 현재 · 격리 daily 진단·근본 수정 / AI 변경 감지 구축 전담`

공유 dev·qa 환경에 의존하던 E2E는 다른 변경의 간섭으로 결과가 흔들렸고, 매번 전체 스위트를 도느라 무인 회귀 검증이 무거웠던 상태였습니다.

- 커밋 시점의 클라이언트·서버·DB를 컨테이너로 묶어, 다른 변경 간섭 없이 결정론적으로 재현 — 공유 환경 의존 제거
- 만성 실패하던 격리 daily를 네 개 독립 원인으로 분리 진단 후 두 건 근본 수정 — 결제 테스트 누락 약 28건을 0으로 정합화(통과 5→14)
- 흩어진 E2E를 모노레포로 통합 — Page Object로 화면 변경을 한 곳에 가두고 세션 자동 발급·재사용으로 중복 제거
- AI 변경 감지: 커밋 diff로 변경 파일을 분석해 QA팀 qase 테스트 케이스 중 연관 케이스만 자동 선별·실행 → Teams 보고 + 실제 회귀/테스트 코드 문제 1차 분류
- 격리 컨테이너가 무거워져 재현용·수정용 이미지 분리를 검토 중 / AI 변경 감지는 daily 실패 누적으로 무인 운영을 멈추고 로컬 주기 실행으로 재편 중 — 구축·운영·한계까지 직접 겪은 영역

`기술` Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted) · Claude Code CLI · Teams Webhook

---

### Electron 빌드·배포·자동 업데이트 인프라 — 담당자 PC 의존을 단독 운영 파이프라인으로
`2024 초기 설계 → 2026 GitHub Actions 이관 · Batch·Linker 공통 인프라 중 최다 기여·단독 운영`

두 데스크톱 앱의 빌드가 담당자 로컬 PC에서 돌려 올리는 구조라, 그 PC가 멈추면 빌드가 멈추고 Windows 서명 USB는 인프라팀을 매번 거쳐야 했던 출시 파이프라인을 재설계했습니다.

- macOS 빌드 파이프라인 재설계로 전체 33~39분을 17~24분으로 56% 단축 — artifact 757→334MB·업로드 114→47초 동반 축소(PR 실측)
- Windows 코드 서명을 물리 빌드머신의 전용 서명 에이전트 풀로 직접 설치·운영 — CI 설정을 넘어 빌드 인프라 자체를 단독 운영하며 서명 USB·인프라팀 의존 제거
- 서명을 빌드 과정 안에 통합 — 빌드 뒤 따로 서명하면 바이너리가 바뀌어 자동 업데이트가 깨지는 문제를 직접 겪은 결과
- macOS 아키텍처를 Universal→arm64→Intel로 11일 만에 3연속 번복 — 용량 2배 트레이드오프를 도입·운영·회수까지 직접 감당한 판단
- blue-green은 도입했으나 green 검증 자동화는 환경·빌드 2배 운영 비용 대비 가치를 따져 의도적 보류

`기술` electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3 · NAS · blue-green

---

### 3D 렌더 품질 자동화 — 색·조명 정공법 재설계 + 자작 visual regression
`2026.04 ~ 현재 · 렌더 정합 재설계·visual regression CI 토대·진입점 단일화 단독`

사내 3D 라이브러리에서 Three.js로 옮긴 뷰어는 두 엔진의 파이프라인 차이로 색·조명이 어긋났고, 초기엔 감마 보정값으로 그 차이를 덮은 상태였습니다.

- 색·조명 정합을 감마 우회에서 SRGB 정공법으로 재설계 — 차이를 덮던 보정값을 폐기하고 약 1,420줄 제거
- 일반 픽셀 비교가 못 잡는 mesh 렌더 회귀를, 채널별 평균 픽셀 차이를 metric으로 정의한 자작 도구로 가드하는 visual regression CI 구축
- 소프트웨어 렌더러로 CI·로컬 GPU 차이를 흡수해 결정적으로 측정, 3개 앱 공유 baseline을 단일 metric 세트로 운영(4 케이스 × 3 쌍 = 12 metric)
- 뷰어 mesh 변환을 단일 진입점으로 통합 — 사전 로딩·폴백·향후 추가 경로가 모두 한 경로를 통과하도록 재설계
- 썸네일 엔진의 Three.js 통합은 번들·성능 이득을 실측으로 검증한 결과 이득이 없어 보류 — '안 하는 결정'을 데이터로 정당화

`기술` Three.js · draco3d · Playwright · pngjs · swiftshader · WebGL · SRGB ColorManagement · Vitest

---

### 관측성·ErrorBoundary 표준 정렬 — 5개 앱을 팀 Datadog 표준에 정렬
`2026.04 ~ 현재 · EB→Datadog 정렬 종합 계획 author·5앱 통합 단독 주도`

에러는 잡히지만 어디서 왜 났는지 추적되지 않던 상태. 같은 안전망이 모든 실패를 한 메시지로 흡수했고, 앱마다 ErrorBoundary와 관측 부착 방식이 제각각이었습니다.

- 3-layer ErrorBoundary 아키텍처(Root→Section→Feature) 설계·확립, 5개 앱 통합(4개 완전 + crown 부분)
- EB→Datadog 표준 정렬 종합 계획을 author로 주도 — 의사결정 7차 개정·5개 PR 분해·EB↔Datadog 책임 분리
- 한 에러 메시지로 collapse되던 Export 실패 124건을, 커스텀 에러 클래스와 RUM 직렬화로 root cause까지 추적 가능하게 재구성
- 직접 추가한 7분류 에러 체계가 특정 앱에 편향됐다고 보고, 팀 Datadog 가시성 표준에 수렴시키며 자기 분류를 스스로 폐기 (관측 어휘 정의는 팀, 본인은 EB를 그 어휘에 정렬)

`기술` React · TypeScript · ErrorBoundary · Datadog RUM · 모노레포(NX)

---

### Dentbird Account — B2B SaaS 구독·결제 FE
`2023.09 ~ 2025.07 · 구독제 전환 FE 전담 설계·구현`

일회성 크레딧 결제에서 반복 구독 매출로 비즈니스 모델을 전환하며, 글로벌 멀티테넌트 환경의 결제·권한 도메인 FE를 전담. 결제는 정확성이 곧 신뢰라, 기능 구현을 넘어 부분 실패에도 무너지지 않는 검증 구조까지 함께 설계했습니다.

- 플랜 업그레이드·시트 구매·구독 취소/재개·쿠폰·결제수단·히스토리까지 구독 워크플로우 전체를 레거시 크레딧 결제와 공존시키며 전담 구현
- 기존 별도 결제 팝업 앱에 Stripe 결제 페이지를 추가, 부모↔팝업 origin 격리 구조 위에서 결제 컨텍스트만 안전하게 주고받도록 연동
- HTTP 200으로 내려오는 서버의 비즈니스 에러를 커스텀 에러로 코드별 분기 — 결제 정보를 못 받아도 내역 조회·구독 취소·뒤로가기는 살아남도록 Fault Tolerance를 도메인 의존 관계 기준으로 설계
- 결제 상태 동기화의 무한 폴링을 폴링 안전장치로 교정 — 기본 3초 간격·최대 20회 후 종료·언마운트 시 정리
- FSD·패턴을 처음 본격 도입하며 과적용을 경험한 끝에 "기술 도입은 일관성이 아니라 우선순위 높은 문제부터"로 수렴

`기술` React · TypeScript · react-query · Stripe · FSD · ErrorBoundary · Playwright(E2E)

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript (ES6+) |
| **Frontend** | React 18/19, Next.js |
| **State / UI** | TanStack Query, Recoil · MUI, Emotion |
| **Desktop** | Electron (IPC, Deep Link, Custom Protocol, Auto Update, Code Signing) |
| **3D / Render** | Three.js, draco3d, WebGL, SRGB ColorManagement |
| **Build / Arch** | NX, pnpm, Git Subtree, Module Federation, FSD, 런타임 Config |
| **Testing** | Playwright, Vitest, pngjs(visual regression), MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs, ErrorBoundary |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

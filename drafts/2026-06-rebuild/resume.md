# 김진완 (Jinwan Kim) | Frontend Developer

> **화면 너머 제품의 구조와 품질을 끝까지 책임지는 프론트엔드 개발자**
> 웹↔로컬 디바이스 경계, 모노레포 토대, 3D 렌더 품질, 관측 표준까지 직접 설계·운영하고, 자기 결정의 후과(부채·자기폐기)까지 추적합니다.

- [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 자기소개

2023.09부터 AI 기반 치과 CAD/CAM SaaS(Dentbird)에서, 화면 구현을 넘어 **제품이 돌아가는 구조와 품질을 끝까지 책임지는** 프론트엔드 개발자로 일해왔습니다.

- **범위** → FE + Electron 데스크톱(빌드·코드 서명·배포 인프라 단독 운영) + 일부 백엔드 풀스택
- **구조** → 도메인 통합 라이브러리로 런타임 환경·격리 재현의 토대 · NX 모노레포 이관(Git Subtree) · 웹↔로컬·렌더 엔진·관측의 책임 경계 설계
- **판단** → LNA 우회 20여 방안을 탐색해 장기 안정성 기준으로 Custom Protocol 채택 · 3D 엔진 통합 보류를 실측으로 정당화 · 자기 분류를 표준에 맞춰 스스로 폐기
- **효율** → Electron 빌드 시간 56%↓(실측) · 커밋 단위 격리 재현 · 픽셀 비교가 못 잡는 mesh 회귀를 자작 metric으로 차단 · AI 변경 감지로 회귀 조기 탐지

---

## 핵심 역량

- **경계 설계**: 웹↔로컬 디바이스 브리지(Chrome LNA 대응), 부모↔팝업 origin 격리, 두 3D 렌더 엔진 책임 경계
- **플랫폼 아키텍처**: 도메인 통합 라이브러리(런타임 환경·격리 재현 토대), NX 모노레포 이관, Micro Frontend
- **품질 자동화**: Playwright E2E, 커밋 단위 컨테이너 격리 재현, AI 기반 변경 감지, 자작 픽셀 metric visual regression
- **데스크톱**: Electron 프로덕션 릴리스, self-hosted 빌드머신·서명 에이전트 풀 단독 운영
- **관측성**: 3-layer ErrorBoundary, Datadog RUM 표준 정렬

---

## 경력

**이마고웍스 (ImagoWorks)** | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

AI 기반 치과 CAD/CAM SaaS(Dentbird)의 프론트엔드를 설계·구축했습니다. 도메인 통합 라이브러리로 플랫폼 토대를 깔고, 웹↔로컬 디바이스 연동, 3D 렌더 품질 자동화, 격리 재현 기반 품질 인프라, 관측 표준 정렬, Electron 빌드·배포 인프라까지 폭넓은 기술 결정을 맡았습니다.

---

## 프로젝트

### Dentbird Linker — 웹↔로컬 CAM 디바이스 브리지
`2024.07 ~ 2025.12 (이후 Solutions 통합, 2026.06까지 단독 운영)`

자체 로컬 서버가 없는 CAM까지 연동해야 하고 Chrome LNA가 브라우저→로컬 통신을 막은 상황에서, 웹↔로컬 연동을 Custom Protocol 중간 레이어로 재설계했습니다.

- Chrome LNA로 막힌 웹↔로컬 통신을 **20여 방안 탐색 끝에 Custom Protocol 중간 레이어로 재설계** *(기존 로컬 서버 방식 폐기 — 브라우저가 앱을 깨워 클라우드 파일 self-fetch → 변환 후 CAM 실행)*
- 이질적인 외부 CAM **12종**의 좌표계·전달 방식 차이를 단일 변환·연동 인터페이스로 추상화
- 명확한 스펙이 없는 기존 연동을 특성화 테스트로 고정한 뒤 구현으로 통과 *(버그 수정이 곧 살아있는 스펙)*
- **판단·지속성**: WebSocket은 '곧 LNA 적용 예정'이라 재차단 우려로 탈락 — 장기 안정성 기준 채택. 데이터 전달은 풀었으나 양방향성을 포기한 트레이드오프가 감지 채널 부채로 남아 개선 검토 중

### DentBird Solutions 플랫폼 토대 — 도메인 통합 라이브러리 · 모노레포 이관
`2024.06 ~ 현재`

환경별로 흩어진 도메인·URL 구성을 통합 라이브러리로 일원화해, 팀의 런타임 환경 분리·격리 재현 환경이 그 위에 쌓이는 토대를 깔았습니다.

- 도메인 통합 라이브러리 설계 — 런타임 환경 분리·격리 재현 환경이 얹히는 **시작점** *(런타임 환경 분리 자체는 팀 공동)*
- 별도 레포의 앱 2종·공용 라이브러리 6종을 메인 NX 모노레포로 이관 *(Git Subtree Split으로 이력 보존 · 네임스페이스 리네임 · 정기 동기화 운영)*
- NX 빌드 오류를 진단, 불필요한 산출물 설정 제거 *(빌드 태스크 11개→9개, OOM 해결)*
- **판단**: 모노레포 도구를 새로 고르지 않고 기존 NX 유지 — 전환 이득이 이관·재학습 비용을 넘지 못한다고 본 비용-편익 판단

### 3D 렌더 품질 자동화 — 정공법 재설계 + 자작 visual regression
`2026.04 ~ 현재`

Three.js로 옮긴 뷰어의 색·조명 차이를 감마 우회로 덮던 상태를 SRGB 정공법으로 다시 풀고, 일반 픽셀 비교가 못 잡는 mesh 렌더 회귀를 자작 metric으로 가드했습니다.

- 색·조명 정합을 감마 우회에서 SRGB 정공법으로 재설계 *(메인 스레드 WASM 디코더 폐기 등 ~1,420줄 제거)*
- mesh 렌더 회귀를 자작 픽셀 metric으로 가드하는 visual regression CI 구축 *(소프트웨어 렌더러로 CI·로컬 GPU 차 흡수 · 정상 평균 0.0 vs 회귀 2.2865로 RED 차단)*
- 3개 앱 공유 baseline을 단일 metric 세트로 운영 *(4 케이스 × 3 쌍 = 12 metric)*
- **판단**: VTK 썸네일 엔진을 Three.js로 통합하지 않기로 결정 — 번들 이득을 실측했으나 0이라 책임 경계 공존으로 결론, **'안 하는 결정'을 데이터로 정당화**

### 품질 자동화 토대 — 커밋 단위 격리 재현 · E2E 통합 · AI 변경 감지
`2025.11 ~ 현재`

다른 변경의 간섭으로 흔들리던 공유 환경 E2E를, 특정 커밋 시점을 결정론적으로 재현하는 격리 환경 위에 올리고 만성 실패를 근본 수정했습니다.

- 커밋 시점의 클라이언트·서버·DB를 컨테이너로 격리 재현 — 다른 변경 간섭 없이 결정론적 재현으로 공유 환경 의존 제거
- 만성 실패하던 격리 daily를 네 개 독립 원인으로 분리 진단·근본 수정 *(결제 테스트 누락 ~28건 → 0, 통과 5 → 14)*
- 실행 인프라를 K8s에서 EC2로 재전환 *(운영 비용 월 ~$150 → ~$30)*
- **AI 변경 감지 E2E**: 커밋 diff에서 변경 파일을 분석해 QA팀 관리 테스트 케이스 중 연관 케이스만 선별·실행 → Teams 보고 + 실제 회귀/테스트 코드 문제 1차 분류 *(EC2 10분 주기 무인 운영)*

### Electron 빌드·배포·자동 업데이트 인프라
`2024 초기 → 2026 GitHub Actions 이관 · Batch·Linker 공통(본인 최다 기여·단독 운영)`

담당자 PC에서 돌리던 두 데스크톱 앱의 빌드를, self-hosted 빌드머신 위에서 코드 서명·자동 업데이트·배포까지 단독 운영하는 파이프라인으로 재설계했습니다.

- macOS 빌드 파이프라인 재설계 *(전체 33~39분 → 17~24분, 56%↓ · artifact 757→334MB · 업로드 114→47초, PR 실측)*
- Windows 코드 서명을 물리 빌드머신의 전용 서명 에이전트 풀로 단독 운영 *(코드 서명 USB·인프라팀 의존 제거)*
- **판단**: macOS 아키텍처를 Universal→arm64→Intel로 11일 만에 3연속 번복(용량 2배 트레이드오프) · blue-green green검증은 운영비 2배 대비 가치로 의도적 보류

### 관측성·ErrorBoundary 표준 정렬
`2026.04 ~ 현재`

에러는 잡히지만 어디서 왜 났는지 추적이 안 되던 상태에서, 5개 앱의 ErrorBoundary를 팀 Datadog 관측 표준에 정렬하는 종합 계획을 author로 세웠습니다.

- 3-layer ErrorBoundary 아키텍처(Root→Section→Feature) 설계, 5개 앱에 통합 *(4개 앱 완전 + 1개 부분)*
- EB→Datadog 표준 정렬 종합 계획서를 author로 작성·주도 *(의사결정 7차 개정 + 5개 PR 분해)*
- Export 실패 가시성 ADR — 한 메시지로 collapse되던 124건을 root cause 추적 가능하게
- **판단**: 직접 추가한 에러 분류(7분류)가 특정 앱에 편향됐다고 보고, 팀 범용 표준에 맞춰 **자기 분류를 스스로 폐기·정렬**

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | TypeScript, JavaScript |
| **Frontend** | React 18/19, Next.js |
| **Desktop** | Electron (Custom Protocol, Auto Update, Code Signing) |
| **3D / Render** | Three.js, draco3d, WebGL, SRGB ColorManagement |
| **Build / Arch** | NX, pnpm, Git Subtree, Module Federation, FSD, 런타임 Config |
| **Testing** | Playwright, Vitest, pngjs(visual regression), MSW |
| **CI/CD · Infra** | GitHub Actions(self-hosted), Azure Pipelines, Docker, AWS(EC2/S3) |
| **Monitoring** | Datadog RUM/Logs, ErrorBoundary |

> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

---

## 교육

**세종대학교** | 데이터사이언스학과 (학사) | 2017.03 ~ 2024.08

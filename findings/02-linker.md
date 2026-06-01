# 02. DentBird Linker — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (1차 발굴 2026-06-01 — 정체성+초기동작+LNA 대응. 빌드/CAM변환은 추후)
> ★★ **용어 정정: PNA → LNA(Local Network Access)가 정답.** PNA(Private Network Access)는 LNA 이전 구글이 도입 시도 후 철회. career/resume의 "PNA" **전부 LNA로 정정 필요** [출처: developer.chrome.com/blog/local-network-access]
> 발굴 대상:
> - `workspace/dentbird-linker` (초기, Bitbucket)
> - `devops/dentbird-linker-app` (Azure DevOps 메인 개발 — jwkim 223커밋 1위)
> - `dentbird-solutions/apps/linker-desktop` (현재, 통합)

---

## 0. 정체성 + 레포 지도

- **Linker = DentBird에서 생성한 케이스(보철물)를 외부 CAM 소프트웨어로 바로 전달하는 데스크톱 앱**
- **1인 개발** `[git: jwkim 223커밋, 2위 37]` — **POC는 타인, 서비스화(0→1 프로덕션)는 본인**
- 레포 진화: `workspace/dentbird-linker`(Bitbucket) → `devops/dentbird-linker-app`(Azure, 2024-08 ~ 2025-12) → `dentbird-solutions/apps/linker-desktop`(현재 통합) `[git]`
- **버전: 8개 태그(v1.0.0 / -0 / v1.0.1 / -0 / v1.0.2 / -0 / -1 / v1.0.3-0) = 메이저 4개** `[git: tag]` → career "8개 버전 + v1.0.0~v1.0.3" 모순 **해소** (8=태그수, 4=메이저)

---

## 1. 의사결정 흔적 (WHY — 문제 실체)

- **불편**: DentBird에서 보철물 생성 → 케이스 export → 브라우저에서 다운로드 → **zip 풀어서 → CAM SW에 수동 투입**. 번거로움
- **기존 해결**: millbox, d+cam 등 **자체 로컬 서버를 제공하는 SW만** "export to cam sw"로 연결
- **Linker 탄생 동기**: **자체 로컬 서버를 제공하지 않는 SW를 지원**하기 위해, 우리가 그 중간을 연결하는 앱이 필요 → Linker

## 2 / 4. 초기 Linker 동작 + 운영 패턴 (HOW)

- **자체 로컬 서버(Express) 열고** 서비스의 "export to cam sw"와 연동 → 케이스 파일 전달받음 `[code: express/multer/jszip]`
- 외부 SW를 Linker에서 **직접 명령어로 실행**, 파일을 명령어로 전달
- **임시파일 관리**: export 파일을 임시경로 보관 → SW 열기 성공 시 삭제 / 미삭제 대비 **최초 실행 시·주기적 클린업** 고민
- **SW 설치여부 + 연결상태를 UI로 관리**: 초기엔 **config json**으로 상태 관리 → 버그 있었음
- **SW 설치경로 자동 탐색**: 기본 디렉토리(기획자와 합의) 하위 탐색
  - SW별 기본 저장경로 우선 확인 → 없으면 **recursive 탐색**
  - 탐색 비용 높음 → **config를 캐싱처럼 신뢰**하되, 실행 실패 시 설치상태 변경 + 유저가 `search`로 재탐색 (캐시 무효화 패턴)

## 5. 기술 스택

- **초기(workspace/dentbird-linker)**: Electron 31, **Vite 5**, React 18, TS 5, **Express 4 + multer(파일 업로드) + jszip(zip) + cors**, electron-builder 24 + electron-forge, electron-updater 6 `[code: package.json]`
- **현재(linker-desktop)**: NX 통합 (`package.json` 없음 → `project.json`/nx). `ARCHITECTURE.md`, `mock-server`, vitest 통합테스트 존재 `[code]`

---

## 6. ★ Chrome LNA 대응 — Custom Protocol 아키텍처 전환 (면접 최강 카드)

### 문제 (WHY)
- **LNA** = 로컬 서버 ↔ 웹 브라우저 통신에 제약. 브라우저 **권한 승인 없이는 로컬서버 통신 불가**
- DentBird "export to cam sw"는 로컬서버 제공 외부 SW + linker와 통신 → **LNA 도입 버전에서 기능 동작 안 함**
- 엄밀히: 권한 허용하면 되지만 **좌상단 권한 팝업을 유저가 놓치는 경우 多 → CS 문의 폭증** `[기억]` (정성적 임팩트)

### 해결 (HOW) — Custom Protocol 중간 레이어
1. 브라우저에서 **커스텀 프로토콜로 linker 실행** + export 파일 정보를 담은 **session id 전달**
2. linker가 그 id로 **자체적으로 우리 서버에 세션 정보 요청** (export 파일 정보 포함)
3. linker가 **파일 직접 다운로드**
4. 기존 export 모듈의 **transformation / construction info 파일 생성** 작업 수행
5. SW 실행
- **결과 아키텍처 변화**:
  - 브라우저 직접 로컬서버 통신 SW → **linker가 직접 로컬서버 통신**으로
  - linker 지원 SW → 바로 파일 넘겨 실행
  - **★ 원래 linker의 Express 로컬서버는 더 이상 사용 안 함** (아키텍처 전환)

### 의사결정: 20가지 방안 분석 → Custom Protocol 채택 `[code: docs/COMPLETE_SOLUTION_ANALYSIS.md, plans/2025-12-02-protocol-spike.md]`
- **20가지 방안 체계 분석** (HTTPS 로컬서버 / WebSocket / Custom Protocol / Chrome Extension / WebRTC / mDNS / QUIC 등) → career "WebSocket vs Custom Protocol POC + 4,000줄"의 실체 (오히려 **축소 표현**)
- **WebSocket(⭐5) vs Custom Protocol(⭐4) 핵심 트레이드오프** `[code: §6.1~6.4 비교표]`:
  - WebSocket: 성능 최고(~2초), 구현 빠름(2-3주), 양방향 — but **"곧 LNA 적용 예정"(1-2년 후 또 막힘) = 단기 해법**
  - Custom Protocol: 약간 느림(~3초), 구현 김(4-6주), 단방향 — but **HTTP 요청이 아니라 영구적 LNA 우회 = 장기 안정성**
  - → **"당장 빠른 WebSocket 대신, 다시 막히지 않을 Custom Protocol 채택" = 장기 관점 의사결정** (면접 최강 답)
- spike(POC) 문서: protocol-spike, port-communication-spike, dcam-http-vs-protocol-comparison 등 실제 검증 기록 `[code]`
- ✅ **확정(사용자)**: WebSocket 대신 Custom Protocol = **장기 안정성 기준** 의사결정 (맞음)
- ✅ **확정(사용자)**: 분석 문서는 **AI 도구 + 본인 혼합**
  - → career 표현을 "4,000줄 문서 작성"이 아니라 **"AI를 활용해 20가지 방안을 폭넓게 탐색하고, 장기 안정성 기준으로 Custom Protocol 의사결정"** 으로
  - 핵심 = 분량 ❌ / **탐색 폭 + 의사결정의 질 + AI 도구 활용 역량** ✅ (자기소개 "AI 도구 운영"과 연결)

### 한계 / 어려움 (성장 서사)
- export → linker 이관 시 기존 동작 동일 재현이 어려워 **버그 많음**
- linker 지원 SW는 OK, but **export에서 직접 보내던 SW**는 배경지식 부족으로 파일 형태/전달 방식 파악 어려움
- **millbox는 라이센스 PC에서만 테스트 가능** → 난이도 ↑
- 각 SW 로컬서버를 **모킹 서버**로 작업 → 통신은 OK, but 파일 형태/이름/필수파일 처리 미비로 버그

### 테스트 전략 (★ 좋은 디테일)
- 방식: **기존 export to cam sw 기능의 단위 테스트를 만들고, linker 구현으로 통과시키기** (characterization test 패턴)
- 한계: 못 잡는 버그 존재 — **명확한 스펙 부재** + 테스트 구현 시 놓침
- 현재: 스펙 추가보다 **버그 해결로 생긴 테스트가 스펙 역할** (test = living spec)
- **e2e 어려움**(Electron + 외부 SW 연동) → **Claude/Codex의 컴퓨터 사용 기능으로 linker e2e 작성 고민 중** (AI 활용)

---

## 미해결 / 추후

| 항목 | 상태 |
|------|------|
| **Chrome LNA 대응** (Custom Protocol 전환) | ✅ 본문 §6 (★ PNA→LNA 정정) |
| **WebSocket vs Custom Protocol** | ✅ 확정: 장기 안정성 기준 채택. 문서=AI+본인 혼합 → "작성" 대신 **"AI 활용 탐색 + 본인 의사결정"** 표현 |
| CAM 좌표계 변환 (16개+ SW) | 📌 판단: 도메인 디테일(`transformMat` 알고리즘)은 **제외**. **"이질적 외부 시스템 16개 통합·추상화 + 검증 전략(모킹/특성화 테스트)"** 으로 LNA 카드에 한 줄 흡수. 독립 발굴 불필요 — 도메인 지식이 아닌 **엔지니어링 패턴**으로 번역 |
| 인스톨러 854→78MB | ✅ **실체 = 초기 번들링 미적용**(소스 코드 그대로 인스톨러에 포함)을 **번들링 적용으로 해결**. career의 "webpack DefinePlugin 환경변수 주입" 설명은 **부정확**. '최적화 성과'가 아닌 **설정 누락 수정** → **이력서/정량표에서 빼거나 약화** (역공 위험: "왜 처음에 번들링 안 했나") |
| 종료 시점 | linker-app 2024-08 ~ 2025-12, 이후 solutions 진행 중(~현재). resume "2026.02" 재확인 |
| 빌드/자동업데이트 | **batch와 공통** → Electron 인프라 통합 정리 대상 |

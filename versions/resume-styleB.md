# 김진완 (Jinwan Kim) — Frontend Developer

**제품의 구조와 품질을 끝까지 책임지는 FE** — React·TypeScript · Electron · 빌드/배포 인프라 · 테스트 자동화
이마고웍스(DentBird, 치과 CAD/CAM SaaS) | 2023.09 ~ 현재 · [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

---

## 핵심 성과 (Highlights)

- **AI 변경 감지 E2E** — EC2의 Claude가 커밋 변경을 분석해 전체 대신 **관련 테스트만 자동 선별·실행**(10분 주기, Teams 보고)하여 회귀를 조기 발견
- **E2E 효율화** — 로그인 세션 공유로 전체 스위트 **18분 → 11분(39%↓)**, Page Object로 중복 셋업 코드 **93%↓**
- **데스크톱 빌드** — 담당자 PC·인프라팀 의존 → self-hosted 파이프라인, **20분 → 6분**(직접 비교)
- **격리 재현 환경** — 특정 커밋의 클라이언트+서버를 Docker로 재현, qa/prod 데이터 복제로 즉시 디버깅
- **레거시 정리** — TypeScript 에러 **688 → 0**, Facade 패턴으로 토큰 관리 **200 → 33줄(85%↓)**
- **Chrome LNA 대응** — 우회 방안 여러 갈래 비교 후 **Custom Protocol** 채택(장기 안정성), 12개 CAM 좌표 변환

---

## 경력 — 이마고웍스 | Frontend Developer | Dentbird 1 Team | 2023.09 ~ 현재

#### Solutions — 플랫폼 인프라 & 품질 자동화 · *효율·구조*
환경변수를 런타임 주입으로 전환해 빌드 1개를 환경별로 재사용하고 **배포 후 핫픽스 반복을 해소**. 그 위에 **커밋 단위 격리 재현 환경**과 **AI·E2E 자동 테스트**를 쌓아 릴리스 신뢰성을 확보. *(런타임 분리·앱 통합으로 "격리하기 쉬운 환경"을 먼저 깐 뒤 자동화를 올린 구조적 접근)*

#### Linker — CAM 연동 Electron 앱 (0→1) · *판단·범위*
웹↔로컬 CAM을 잇는 데스크톱 앱을 설계·릴리스(1개월 113커밋, v1.0.3). Chrome **LNA 규제에 Custom Protocol로 선제 대응**(WebSocket은 향후 재차단 우려로 탈락). **12개 CAM** 좌표 변환, Batch·Linker **빌드 인프라(20→6분)**까지 책임.

#### Account — B2B 구독/결제 FE 전담 · *범위·제품*
크레딧 → **구독제 전환**의 결제·권한 도메인을 전담(15개+ 국가 테넌트·다국어). ErrorBoundary로 **장애 전파 차단**. *"패턴은 우리가 풀려던 문제의 해법이 아니었다"*는 기술 도입 기준을 학습.

#### Micro Frontend — 공통 모듈 통합 · *구조·판단*
4개 서비스 공통 기능을 iframe 런타임 통합으로 모듈화(**변경 시 4곳 수정·배포 → 1곳**). 빌드타임↔iframe을 오가며 *"통합 전략은 조직·배포 제약의 함수"*임을 체득.

---

## 기술 스택

`TypeScript` `React 18/19` `Next.js` · `Electron(IPC·Custom Protocol·Auto Update·Code Signing)` · `NX` `pnpm` `Module Federation` `런타임 Config` · `Playwright` `Jest` `Vitest` `MSW` · `GitHub Actions(self-hosted)` `Docker` `AWS(EC2/S3)` `Datadog`
*(백엔드: Node.js/Fastify·MongoDB — 랜딩 페이지 풀스택 등 일부 직접 개발)*

---

## 교육
**세종대학교** 데이터사이언스학과 (학사) · 2017.03 ~ 2024.08

# 김진완 (Jinwan Kim) — 프론트엔드

> 화면 너머, 제품이 돌아가는 구조와 품질까지 책임지는 프론트엔드 개발자.
> 웹↔로컬 디바이스 경계, 모노레포 토대, 3D 렌더 품질, 관측 표준을 직접 설계·운영합니다.

- [블로그](https://velog.io/@crowwan) · [깃허브](https://github.com/crowwan)

## 기술 스택
TypeScript, React, Next.js, Electron, Three.js, NX, Playwright

## 경력 — 이마고웍스(ImagoWorks)
- Dentbird 1 Team / 프론트엔드 (2023.09~재직) — AI 기반 치과 CAD/CAM SaaS. FE를 넘어 Electron 데스크톱 빌드·배포 인프라 단독 운영, 일부 백엔드까지. 도메인 통합 라이브러리로 플랫폼 토대를 깔고 웹↔로컬 연동·3D 렌더 품질·격리 재현 품질 인프라·관측 표준 정렬·Electron 인프라까지 폭넓은 기술 결정을 맡았습니다.

## 프로젝트

### Dentbird Linker — 웹↔로컬 CAM 디바이스 브리지 (2024.07~진행 중)
Dentbird에서 만든 보철 케이스를 외부 CAM 소프트웨어로 보내는 웹↔로컬 연동. Chrome LNA가 브라우저→로컬 통신을 막으면서 기존 로컬 서버 방식을 Custom Protocol 중간 레이어로 재설계했습니다.
- LNA 우회 방안을 폭넓게 탐색한 뒤, 장기 안정성을 기준으로 Custom Protocol을 채택해 기존 로컬 서버 방식을 대체 (딥링크로 데스크톱 앱을 호출해 세션을 위임하고, 앱이 클라우드에서 직접 파일을 받아 변환 후 CAM 실행)
- 외부 CAM 12종의 좌표계·전달 방식 차이를 단일 변환·연동 인터페이스로 추상화
- 명확한 스펙이 없던 기존 연동을 특성화 테스트로 고정한 뒤 구현으로 통과
- 출시 이후 안정화·연동 확장과 데스크톱 앱 운영 관측까지 2026년까지 단독 책임
- *장기 안정성을 위해 양방향성을 포기한 트레이드오프가 앱 실행 감지 부채로 남아, 감지를 핵심 경로에서 빼는 방향으로 개선 검토 중*
- Electron · Vite · React · TypeScript · Custom Protocol · Datadog

### DentBird Solutions 플랫폼 토대 — 도메인 통합 · 모노레포 이관 (2024.06~진행 중)
환경별로 흩어진 도메인·URL 구성을 통합 라이브러리로 일원화해, 팀의 런타임 환경 분리와 격리 재현 환경이 그 위에 쌓이는 토대를 만들었습니다.
- 도메인 통합 라이브러리를 설계해 흩어진 도메인 파생을 한 곳으로 일원화 — 이후 팀이 진행한 런타임 환경 분리·격리 재현 환경의 시작점
- 별도 레포의 앱 2종·공용 라이브러리 6종을 메인 NX 모노레포로 이관 (Git Subtree Split으로 커밋 이력 보존 · 네임스페이스 리네임 · 정기 동기화 운영)
- 산재한 다국어 관리 스크립트를 중앙화하고, NX 자동 타겟 추론이 일으키던 빌드 오류를 진단·정리 (빌드 태스크 11개→9개, 메모리 부족 해결)
- *모노레포 도구는 기존 NX를 유지 — 전환 이득이 이관·재학습 비용을 넘지 못한다고 본 비용-편익 판단*
- TypeScript · NX 모노레포 · Git Subtree · i18n

### 3D 렌더 품질 자동화 — 색·조명 정공법 + 회귀 검출 (2026.04~진행 중)
Three.js로 옮긴 뷰어의 색·조명 어긋남을 감마 보정으로 덮던 상태를 SRGB 정공법으로 다시 풀고, 일반 픽셀 비교가 못 잡는 mesh 렌더 회귀를 직접 만든 검출 도구로 가드했습니다.
- 색·조명 정합을 감마 우회에서 SRGB 정공법으로 재설계 (색 공간·조명 비율을 명시적으로 맞추고, 텍스처를 표준 디코딩 경로로 정공법 처리하며 약 1,420줄 제거)
- 픽셀 채널 차분 기반 회귀 검출 도구를 직접 구현해 mesh 렌더 회귀를 가드하는 visual regression CI 구축 (소프트웨어 렌더러로 CI와 로컬의 GPU 차이를 흡수해 결정적으로 측정)
- 3개 앱 공유 baseline을 단일 metric 세트로 운영 (4 케이스 × 3 쌍 = 12 metric)
- 뷰어 mesh 변환을 단일 진입점으로 통합 — 사전 로딩·폴백·향후 추가 경로가 한 함수를 통과하도록 재설계
- *VTK 썸네일 엔진을 Three.js로 통합하지 않기로 결정 — 번들 이득을 실측했으나 0이라, '안 하는 결정'을 데이터로 정당화*
- Three.js · draco3d · Playwright · pngjs · WebGL · SRGB ColorManagement

### 품질 자동화 토대 — 격리 재현 · E2E 통합 · AI 변경 감지 (2025.11~진행 중)
공유 환경에 의존하던 E2E가 다른 변경의 간섭으로 흔들리던 상태를, 특정 커밋 시점을 결정론적으로 재현하는 격리 환경 위에 올리고 만성 실패를 근본 수정했습니다.
- 커밋 시점의 클라이언트·서버·DB를 컨테이너로 격리 재현 — 다른 변경의 간섭 없이 결정론적으로 재현해 공유 환경 의존을 제거
- 만성 실패하던 격리 daily를 네 개 독립 원인으로 분리 진단한 뒤 근본 수정 (결제 테스트 누락 약 28건 → 0, 통과 5 → 14)
- 실행 인프라를 K8s에서 EC2로 재전환해 운영 비용을 월 약 $150에서 $30으로 절감
- AI 변경 감지 E2E 구축 — 커밋 diff에서 변경 파일을 분석해 QA팀이 관리하는 테스트 케이스 중 연관 케이스만 선별·실행하고, 결과를 Teams로 보고하며 실패를 실제 회귀와 테스트 코드 문제로 1차 분류
- *컨테이너가 무거워져 성능 개선과 재현용·수정용 이미지 분리를 검토 중. 구축부터 운영·한계까지 직접 겪은 영역*
- Playwright · Docker · MongoDB · EC2 · GitHub Actions(self-hosted) · Claude Code CLI

### Electron 빌드·배포·자동 업데이트 인프라 (2024 초기 → 2026 GitHub Actions 이관)
담당자 PC에서 돌리던 두 데스크톱 앱(Batch·Linker)의 빌드를, self-hosted 빌드머신 위에서 코드 서명·자동 업데이트·배포까지 단독 운영하는 파이프라인으로 재설계했습니다.
- macOS 빌드 파이프라인 재설계 (전체 33~39분 → 17~24분, artifact 757MB → 334MB, 업로드 114초 → 47초, PR 실측)
- Windows 코드 서명을 물리 빌드머신의 전용 서명 에이전트 풀로 단독 운영 (코드 서명 USB·인프라팀 의존 제거)
- 서명을 빌드 과정 안에 포함 — 빌드 뒤 서명하면 바이너리가 바뀌어 자동 업데이트가 깨진다는 걸 직접 부딪혀 학습
- *macOS 빌드 아키텍처를 Universal→arm64→Intel로 11일 만에 3연속 번복(용량 2배 트레이드오프) · blue-green 검증 자동화는 운영비 2배 대비 가치로 의도적 보류*
- electron-builder · electron-updater · GitHub Actions(self-hosted) · Azure Pipelines · 코드 서명·공증 · S3 · blue-green

### 관측성·ErrorBoundary 표준 정렬 (2026.04~진행 중)
에러는 잡히지만 어디서 왜 났는지 추적이 안 되던 상태에서, 5개 앱의 ErrorBoundary를 팀 Datadog 관측 표준에 정렬하는 종합 계획을 직접 작성·주도했습니다.
- 3-layer ErrorBoundary 아키텍처(Root→Section→Feature)를 설계해 5개 앱에 통합 (4개 앱 완전 + 1개 부분)
- ErrorBoundary와 Datadog 관측 표준 정렬 종합 계획서를 직접 작성·주도 (7차에 걸친 의사결정 개정 + 5개 PR 분해)
- 한 메시지로 뭉뚱그려지던 내보내기 실패 124건을 근본 원인까지 추적 가능하게 재구성
- *직접 추가했던 에러 분류(7분류)가 특정 앱에 편향됐다고 보고, 팀 범용 표준에 맞춰 자기 분류를 스스로 폐기·정렬*
- React · TypeScript · ErrorBoundary · Datadog RUM · NX

### Dentbird Account — B2B SaaS 구독·결제 FE (2023.09~2025.07)
일회성 크레딧 결제에서 반복 구독 매출로 비즈니스 모델을 전환하면서, 글로벌 멀티테넌트 환경의 구독·결제 도메인 FE를 전담했습니다.
- 구독 워크플로우 전체를 전담 구현 — 플랜 업그레이드·시트 구매·구독 취소/재개·쿠폰·결제수단·결제 히스토리
- 기존 별도 결제 팝업 앱에 Stripe 결제 페이지를 추가, 부모↔팝업 origin 격리 구조로 결제 컨텍스트만 안전하게 주고받도록 연동
- 서버가 정상 응답으로 내려보내는 비즈니스 에러를 에러 코드별로 분기해, 결제 정보를 못 받아도 결제 내역·구독 취소 같은 핵심 흐름은 살아남도록 설계
- 결제 상태 동기화의 무한 폴링을 안전장치로 교정하고, 구독 회귀 가드까지 직접 작성해 구현부터 품질까지 종단 책임
- React · TypeScript · react-query · Stripe · FSD · ErrorBoundary · Playwright

## 교육
세종대학교 데이터사이언스학과 (학사) · 2017.03~2024.08

## 기술 스택 (전체)
TypeScript, JavaScript · React 18/19, Next.js · Electron(Custom Protocol/Auto Update/Code Signing) · Three.js/draco3d/WebGL · NX/pnpm/Git Subtree/Module Federation/FSD · Playwright/Vitest/MSW · GitHub Actions(self-hosted)/Docker/AWS · Datadog RUM
> 백엔드(Node.js/Fastify·MongoDB)는 기업 랜딩 페이지 풀스택 등에서 일부 직접 개발했습니다.

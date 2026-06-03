# 09. DentBird Solutions ② — 테스트 자동화 (E2E 본격 도입 + Claude 자동 E2E) — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (코드·vault·git 광범위 발굴 완료 / 일부 정량·기여분 확인)
> 발굴 대상: `~/Works/devops/dentbird-solutions/apps/e2e`, `libs/e2e`, `.claude/skills/e2e`, vault
>
> 01 Solutions의 형제 카드(주제 분리). 이 파일 = **테스트 자동화**. 01(런타임config+격리재현)의 격리 스택 위에서 E2E가 돌고, 그 위에 Claude 자동 실행을 올린 구조 → **01 → 09 연결**.

---

## A. E2E 테스트 본격 도입 (Playwright)

### 1. 의사결정 흔적
- **출발점**: 흩어져 있던 E2E를 모노레포로 통합 — `feat: Integrate E2E tests from separate repositories`(본인). [git: 본인]
- **Page Object 패턴 채택**: `BasePage` 추상 클래스 + 제품별 POM으로 중복 제거·UI 변경 국소화. [code: libs/e2e/shared/src/pages/BasePage.ts]
- **세션 자동 관리 설계**: 로컬 → TC Manager 서버 fetch → 없으면 브라우저 로그인 후 push → 만료 시 재생성. `auth_lastActivity` 갱신으로 IdleTracker 로그아웃 방지. [code: apps/e2e/*/global-setup.ts, shared/auth.setup.ts]
- **시각 회귀(visual regression) 도입**: Playwright `toHaveScreenshot()` + SwiftShader, `maxDiffPixelRatio: 0.1`. mesh 렌더(posterior/bridge/inlay-onlay/implant-crown), 케이스 썸네일, Three.js mesh. [code: apps/e2e/cloud/tests/viewer/render-visual.test.ts, snapshots/*.png]

### 2. 측정 가능한 변화
- **E2E 스펙 약 642개, 13개 앱/제품** 커버(cloud 255, batch 83, login-matrix 69, modeler 41, backoffice 40, billing 35, crown-cloud 31, account 24, milling 22, crown 18, wip-lock 16 등). [code: apps/e2e/*/tests]
- 본인 E2E 관련 커밋 다수(누적). [git: 본인 — 수치는 author 집계 방식 따라 변동, 정확값 추후] [기여확인]
- Page Object 계층화로 공통 플로우(로그인·스피너·세션·alert) 단일화 → 중복 제거. [추론 + code]

### 3. 후속 영향
- E2E가 격리 스택(docker/e2e, `STAGE=local`) 위에서 실행 → 01 격리재현과 결합. [code: docker/e2e/docker-compose.private-dns.yml]
- 결과 보고 파이프라인: JUnit XML(GitHub Checks) + HTML 리포트 + **TC Manager 커스텀 리포터**(`libs/e2e/tc-manager-reporter`) + Qase 동기화(Crown) + Teams 알림. [code]
- 시각 회귀가 cross-app mesh 정합 가드로 확장(DEN-4539/4549, Three.js 전환 검증과 맞물림). [git: 본인]

### 4. 운영 패턴 / 기여 주체
- **본인 주도** [본인]: 별도 레포 E2E 모노레포 통합, 초기 Backoffice/Account E2E 인프라(D1-775 등), 이후 제품 전반 확대·회귀 가드 추가. E2E 가이드 문서화(`apps/e2e/CLAUDE.md` 등).
- self-hosted runner(crown_test Windows bare-metal, build-server) 기반 CI 다수 워크플로(test-crown-e2e, e2e-local-daily, login-matrix-full-set, smoke-post-deploy 등). [code: .github/workflows]

---

## B. EC2 Claude 자동 E2E ("e2e detect")

### 1. 의사결정 흔적 / 동작 (사용자 설명과 일치)
- **목표**: EC2에 Claude를 띄워, **크론으로 레포 pull → 변경 사항을 Claude가 분석 → 관련 E2E를 직접 골라 실행 → 팀 Teams로 결과 보고**. [기억] + [code: .claude/skills/e2e/SKILL.md]
- 구현: `e2e detect` 스킬 — git 변경 감지(State file로 중복 방지) → Claude가 코드 분석으로 관련 TC 추론 → `playwright test --grep "Qase ID: N)"` 실행 → 결과 분석 → Teams webhook(Power Automate) 전송. [code: .claude/skills/e2e/SKILL.md L618~1145]
- 스케줄: EC2 크론 `*/10 * * * *`(업무시간) + `scheduler.mjs`가 평일 08:00·13:00 KST 트리거(한국 공휴일 배열 포함). [code: .claude/scheduler/scheduler.mjs]
- 안전장치: lockfile+flock 동시 실행 차단, 110분 타임아웃, State file로 동일 커밋 중복 분석 방지. [code]

### 2. 측정 가능한 변화 / 보고
- Teams 메시지: Adaptive Card(passed/failed/total + Claude 진단 요약 + 리포트 링크). [code: scripts/medic/send-report.js, SKILL.md]

### 3. 후속 영향 / 지속성·한계 (★ 성장 서사)
- **현재 미사용**: ① EC2 인스턴스 보안 정책으로 TC Manager API 접근 불가 → Teams 웹훅으로 회귀, ② 2026-05-28부터 daily schedule run 실패 누적. → **`e2e-local-daily.yml` 재활성화 프로젝트로 전환**(3층위: 인프라 포트 누수 / fixture drift / 보고 채널). [vault: 2026-W23-e2e-daily-revival.md, docs/plans/2025-12-01-e2e-change-detection-cronjob.md]
- 즉 "AI로 변경 기반 E2E 자동 선별·실행·보고"를 **실제로 구축해 돌려봤고, 운영 한계(보안·안정성)까지 겪은** 경험 → 정직한 성장 서사.

### 4. 기여 주체
- **본인 주도** [본인]: 계획서(D1-987), 스킬 문서·프롬프트(D1-992/993/997), 크론 브랜치 비교 개선(D1-1000), Teams 통합. [git: 본인]

---

## 미해결 (출처 확인 필요)

- **본인 E2E 기여 정량**: 누적 커밋 수/본인이 작성한 스펙 비중 정확값. (642는 현재 전체) [기여확인]
- **E2E 도입 전/후 효과**: 도입 전 수동 QA 부담, 도입 후 회귀 탐지 체감(정성). 면접 답변용 구체 사례. [기억]
- **Claude 자동 E2E의 효과 기간**: 실제 운영했던 기간·그동안 잡은 회귀 사례 1~2개. [기억]
- E2E "본격 도입"의 WHY 한 겹 더: 왜 그 시점에 본인이 주도했나(QA 협업/사고 계기?). [기억]

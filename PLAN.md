# Resume Repo — 작업 계획서

> 1년 내 이직 목표(라인/오늘의집/CJ ENM) 기준, 이력서·경력기술서·면접 답변 자산을 **콘텐츠 먼저, 포맷은 나중에** 정비합니다.
>
> 운영 룰은 [CLAUDE.md](./CLAUDE.md), 인터뷰 진행은 [interviews/README.md](./interviews/README.md).

---

## 현재 상태

- **Current Phase**: **Phase 0A — Findings 수집** 진입 대기
- **Active Card**: (없음)
- **다음 Phase**: Phase 0B (일관성 오류 정리 — Findings 산물 기반)

> Claude는 작업 시작 시 이 줄을 먼저 확인하고, 다음 Phase로 진입 시 이 블록을 갱신합니다.

---

## 3단계 흐름 (한눈에)

```
findings/0X-*.md          (raw 사실 — Phase 0A에서 채움)
    ↓
interviews/0X-*.md        (4축 서사 — Phase 2에서 채움)
    ↓
resume.md / career-description.md   (형식화 — Phase 2~4)
```

각 단계는 다음 단계의 **유일한 입력원**입니다. findings를 건너뛰고 인터뷰로 가면 4축의 사실 정보가 사용자 기억에만 의존하게 됩니다.

---

## Pre-Phase — Git Identity 격리 (완료)

| 항목 | 결과 | Status |
|------|------|--------|
| `user.name` (local) | `Jinwan Kim` | ✅ |
| `user.email` (local) | `32172056+crowwan@users.noreply.github.com` (개인 GitHub noreply) | ✅ |
| 격리 범위 | 이 레포만 (`git config --local`) | ✅ |
| `.git/hooks/pre-commit` | 회사 도메인(`imagoworks.ai`) author 자동 차단 + dry test 양쪽 케이스 통과 | ✅ |
| 과거 5개 커밋 | 회사 이메일로 박혀 있음. 의도적으로 그대로 둠 (history rewrite 안 함) | ⚠️ 알고 있음 |

> 새 워킹트리/재클론 후엔 `CLAUDE.md § 8 Git Identity 셋업`의 명령을 다시 실행해야 합니다.

---

## Phase 0A — Findings 수집 (★ 시작 지점)

**목표**: 본문/인터뷰 작업 전에, 실제 작업 레포·vault·기억에서 **의미있는 사실만** 카드별로 발굴해 `findings/0X-*.md`에 누적합니다. 등급/그룹화는 여기서 안 함.

**왜 이 단계가 먼저인가**:
- 현재 이력서·career에 **임의 수치·모호 표현**이 일부 박혀 있음 (Phase 0B 항목 = 그 증거)
- 4축 인터뷰의 사실 정보를 사용자 기억에만 의존하면, 또 같은 오류가 재생산됨
- vault `projects/dentbird-solutions/`에 이미 풍부한 자료(`log`/`learnings`/`decisions`/`bugs`)가 있어 발굴 비용이 낮음

**수집 기준**: [findings/README.md](./findings/README.md) — 4가지 시그널(의사결정 / 측정 / 후속 영향 / 운영 패턴) 중 하나라도 걸리면 수집. 출처 태그 강제.

**진행 순서**:

| 순 | 카드 | 발굴 대상 | Status |
|----|------|----------|--------|
| 1 | 01. Solutions | `dentbird-solutions`, vault 풍부 | ⬜ |
| 2 | 02. Linker | `dentbird-linker-app`, vault 확인 필요 | ⬜ |
| 3 | 03. MFE | `dentbird-front-module-monorepo`, `dentbird-console-client` | ⬜ |
| 4 | 04. Account | `dentbird-account-client` | ⬜ |
| 5 | 05. Batch | 모노레포 통합 전 별도 레포 확인 필요 | ⬜ |
| 6 | 06. Landing | `landing-page-client`, `landing-page-server` | ⬜ |
| 7 | 07. Design System | `imago-design-system` | ⬜ |
| 8 | 99. Misc | Datadog 스터디 등 카드 외 경험 | ⬜ |

**완료 기준**: 카드별 4시그널 섹션이 어느 정도 채워지고, `[측정필요]`/`[기억]` 중 본문 박을 항목은 별도 확인 완료. 이때 Phase 0B의 3가지 의문(Linker 종료·버전·LCP)은 자동 해결될 가능성 큼.

---

## Phase 0B — 일관성 오류 정리 (Findings 산물 기반)

**목표**: Phase 0A에서 확인된 정확값으로 두 문서 간 불일치를 1 PR로 정리.

| 항목 | 위치 | 해야 할 일 | Status |
|------|------|------------|--------|
| Chrome LNA → PNA | `resume.md:11` | `LNA` → `PNA`. career는 이미 PNA로 통일됨 | ⬜ |
| 연차 표현 | `resume.md:7` `3년차` | 2023.09 입사 → 2026.05 기준 **2년 8개월**. 정확값으로 교체 | ⬜ |
| Linker 기간 | resume `2024.08 ~ 2026.02` vs career `~ 현재` | Phase 0A 산물(`findings/02-linker.md`)에서 확인된 종료 시점으로 통일 | ⬜ |
| Linker 버전 수 | career `v1.0.0~v1.0.3` + `8개 버전` | Phase 0A에서 git tag 발굴 결과로 확정 | ⬜ |
| 랜딩페이지 정량값 | resume.md 6번, career 6-4 | Phase 0A 산물에서 확인된 LCP/Lighthouse 실수치 또는 표현 완화 | ⬜ |

**PR 제목 예시**: `docs: 이력서/경력기술서 일관성 오류 정리 (LNA→PNA, 연차, Linker 기간)`

---

## Phase 1 — 누락 큼지막한 경험 슬롯 만들기

**목표**: 본문에 아예 빠져 있는 큰 경험에 자리를 마련하고, 짧은 인터뷰로 채움.

| 항목 | 내용 | Status |
|------|------|--------|
| Datadog 팀 옵저버빌리티 스터디 리딩 | ~10명, 6주 추정 — 정확값은 사용자 확인. `99-misc.md`로 인터뷰 후 career에 신규 섹션 추가 (또는 Solutions 카드 안에 흡수) | ⬜ |
| AI 도구 활용 강조 | Claude Code CLI 변경 감지 시스템은 이미 본문 있음. 자기소개와 핵심성과에서 "AI 도구 운영" 키워드를 격상 (Phase 4에서 통합 처리) | ⬜ |

Phase 0과 묶어도 됨 (사용자 선택).

---

## Phase 2 — 메인 카드 3개 깊은 4축 인터뷰

각 1 PR. **입력: `findings/0X-*.md`** → 4축 빈칸은 사용자 기억으로 보강 → `interviews/0X-*.md` 저장 → career 압축 → resume 정렬.

### Phase 2A — DentBird Solutions ★ (시작 지점)

**왜 먼저**: 현재 진행 중이라 기억 가장 생생. 모노레포/E2E/Datadog 시장 키워드 밀집.

**인터뷰 깊이 가이드**:
- 모노레포 통합: 분산 5개를 왜 통합? "공통화 좋아 보이니까"가 아니라 진짜 아팠던 사고/낭비
- Playwright + Page Object: 93% 중복 감소·39% 성능 개선의 결정 근거. 다른 패턴은 왜 탈락
- Claude Code CLI 기반 변경 감지: 시간/비용/안정성 트레이드오프. 도입 후 효과 측정 방식
- Datadog RUM 전사 적용: 누가 처음 제안? 도입 전후 장애 추적이 어떻게 바뀜?
- Facade 토큰 관리 200→33줄: 단순 양적 감소가 아니라 Race Condition / Stale Closure가 왜 발생했는지

**산출물**: `interviews/01-solutions.md`, career § Solutions 재작성, resume § 1 재작성

| Status | ⬜ |
|---|---|

---

### Phase 2B — DentBird Linker

**왜 중요**: WebSocket vs Custom Protocol POC가 면접에서 가장 강한 카드(기술 의사결정 + 4,000줄 문서).

**인터뷰 깊이 가이드**:
- 854MB → 78MB (91%↓): webpack DefinePlugin 환경변수 빌드 시점 주입의 진짜 효과. 어떤 자원이 빠진 건지
- Chrome PNA 대응: 언제·어떻게 감지? 선제 대응의 트리거는?
- WebSocket vs Custom Protocol POC: 두 안의 측정 방식, 채택 근거, 탈락 근거
- 16개+ CAM SW 좌표계 변환 매트릭스: 알고리즘 자체보다, **검증 방식**(어떻게 맞는지 알았나)
- 1개월 113커밋 0→1 개발: 페이스가 가능했던 이유, 못 한 일은 무엇

**산출물**: `interviews/02-linker.md`, career § Linker, resume § 2

| Status | ⬜ |
|---|---|

---

### Phase 2C — Micro Frontend 전환

**왜 중요**: iframe → Module Federation 재설계 = 성장 서사의 정석. 사용자가 가장 강조하라고 한 카드.

**인터뷰 깊이 가이드**:
- iframe MFE 4(or 5)개 운영의 진짜 불편: "개별 도메인"이 정확히 어떻게 비용? SSL 비용? 보안 검토 반복?
- Module Federation 결정 근세: Web Components / 단일 SPA 통합 / Nx 라이브러리 분할 같은 대안은?
- FSD 도입: 왜 FSD? 도입 후 코드 리뷰/온보딩이 실제로 바뀌었나?
- 3주 4개 모듈 신규 개발: 페이스 가능했던 구조적 이유
- 지속성: 지금도 MF로 굴러가나? Phase 3 / 4 재설계 계획 있나?

**산출물**: `interviews/03-mfe.md`, career § MFE, resume § 4

| Status | ⬜ |
|---|---|

---

## Phase 3 — 나머지 4개 카드 가벼운 4축

각 1 PR 또는 묶음 PR (사용자 선택). 인터뷰 깊이는 메인 카드의 절반.

| 카드 | 인터뷰 파일 | Status |
|------|-------------|--------|
| DentBird Account (구독/계정) | `04-account.md` | ⬜ |
| DentBird Batch (Electron 1인 개발) | `05-batch.md` | ⬜ |
| 기업 랜딩 페이지 (Next.js 풀스택) | `06-landing.md` | ⬜ |
| Imago Cloud Design System | `07-design-system.md` | ⬜ |

각 카드의 4축 답이 얕더라도 OK. 핵심은 "이게 작업 나열이 아니라 결정 서사로 보이는가"입니다.

---

## Phase 4 — 자기소개·핵심성과·키워드 정렬

**목표**: 메인 카드 인터뷰가 끝나 본문 디테일이 강해지면, 상단 요약을 다시 씁니다.

| 항목 | Status |
|------|--------|
| `resume.md` 자기소개 재작성 (실제 강점 3개로 압축, "AI 도구 운영" 격상) | ⬜ |
| `career-description.md` 핵심 성과 5줄 재작성 | ⬜ |
| 정량적 성과 종합 표 갱신 (인터뷰에서 정정된 수치 반영) | ⬜ |
| 공고 키워드 매핑 체크 (성능 지표화·옵저버빌리티·아키텍처 주도·AI 도구 활용·모노레포·테스트 자동화·CS 기초) | ⬜ |

---

## Phase 5 — 포맷·배포 손질 (선택)

콘텐츠 안정화 후 진입.

| 항목 | Status |
|------|--------|
| Pages 배포 검증 ([github.com/crowwan/resume](https://github.com/crowwan/resume) Pages URL 확인) | ⬜ |
| PDF 자동 빌드 복원 — README에는 `build-pdf.yml` 언급되나 실제 워크플로우는 `deploy-pages.yml` 뿐. 필요 시 PDF job 추가 | ⬜ |
| Pages 공개 시 개인정보 분리 (연락처 분리 / private repo 유지 / public Pages의 검색 노출 절충) | ⬜ |
| `resume-style.css` 다듬기 (필요 시) | ⬜ |

---

## 진행 규칙 요약

1. **순서대로 진행** (Phase 0A → 0B → 1 → 2A → 2B → 2C → 3 → 4 → 5).
2. 사용자가 명시적으로 점프하라고 하면 점프 가능.
3. 각 Phase 완료 시 이 PLAN.md 상단의 **Current Phase** 줄을 갱신.
4. **사실 발굴은** [findings/README.md](./findings/README.md)의 4시그널 기준을 따름.
5. **인터뷰 진행은** [interviews/README.md](./interviews/README.md)의 4축 템플릿을 따름. 입력은 해당 카드의 `findings/0X-*.md`.
6. Phase 2부터는 **한 PR = 한 카드**.
7. **새 사실/수치를 본문에 박을 때마다** "이거 사용자가 답했나, 내가 만들었나" 자체 검증. findings에 출처 없는 항목은 본문 못 박음.

# Resume Repo — 작업 계획서

> 1년 내 이직 목표(라인/오늘의집/CJ ENM) 기준. **정보 정리 → 포지셔닝 기반 새 이력서 → 비판적 평가 루프**로 "매력적인" 이력서를 만든다.
>
> 운영 룰: [CLAUDE.md](./CLAUDE.md) · 작성 기준: [WRITING-GUIDE.md](./WRITING-GUIDE.md) · 발굴 룰: [findings/README.md](./findings/README.md) · 인터뷰: [interviews/README.md](./interviews/README.md)

---

## 현재 상태

- **Current Phase**: A(정보 토대) — 미완 5개 카드 findings 발굴 잔여. (B 기준 정비는 ✅ 완료 — 포지셔닝 섹션 + EVALUATION-RUBRIC)
- **워크플로 전환 (2026-06-01)**: 기존 "카드별 순차 발굴→인터뷰→재작성"에서 → **[전체 정보 정리 → 포지셔닝 기반 백지 재작성 → 평가 서브에이전트 루프]** 로 전환. *기존 양식을 답습하면 매력이 안 생긴다*는 판단에서 비롯.
- **포지셔닝 1안 (검증 대상)**: "제품의 구조와 품질을 끝까지 책임지는 FE" + 4축(**범위 / 구조 / 판단 / 효율**). 확정 아님 — Phase D 평가 루프에서 검증·조정.
- **완료**: ✅ 인프라 통합(career/resume 프로젝트 6) + 가이드 도입(WRITING-GUIDE) + 운영문서 등록
- **interviews 트랙**: 이력서와 **병행 유지** (면접 답변 스크립트 자산 + 카드 서사 깊이)

> Claude는 작업 시작 시 이 줄을 먼저 확인하고, Phase 진입 시 이 블록을 갱신합니다.

---

## 새 워크플로 (한눈에)

```
A. 정보 토대   findings/ 전체 완비 (raw 사실, 출처 강제)
      ↓
B. 기준 정비   WRITING-GUIDE(포지셔닝 섹션) + EVALUATION-RUBRIC
      ↓
C. 새 이력서   findings → (interviews 4축 병행) → 포지셔닝 렌즈로 백지 재작성
      ↓
D. 평가 루프   평가 서브에이전트가 RUBRIC으로 비판적 채점 → 통과까지 개선
      ↓
E. 포맷·배포   HTML/Pages, JD별 버전
```

**핵심 원칙**: 기존 resume/career 양식을 **답습하지 않는다.** findings(사실)와 포지셔닝에서 새로 짓고, 비판적 평가자가 `통과`시킬 때까지 돌린다. 사실·수치는 여전히 **발굴 OK / 발명 NO** (CLAUDE.md §3).

---

## Pre-Phase — Git Identity 격리 (완료)

| 항목 | 결과 | Status |
|------|------|--------|
| `user.name` (local) | `Jinwan Kim` | ✅ |
| `user.email` (local) | `32172056+crowwan@users.noreply.github.com` (개인 GitHub noreply) | ✅ |
| 격리 범위 | 이 레포만 (`git config --local`) | ✅ |
| `.git/hooks/pre-commit` | 회사 도메인(`imagoworks.ai`) author 자동 차단 | ✅ |

> 새 워킹트리/재클론 후엔 `CLAUDE.md § 8 Git Identity 셋업` 명령을 다시 실행.

---

## Phase A — 정보 토대 (findings 완비) ★ 현재

모든 카드의 raw 사실을 `findings/`에 완비. 서사 X, 출처 태그 강제. 기준: [findings/README.md](./findings/README.md)의 4시그널.

| 카드 | 발굴 대상 (`~/Works/devops/`, `~/Works/workspace/`) | Status |
|------|------|--------|
| 01. Solutions | `dentbird-solutions`, vault 풍부 | ⬜ 발굴 |
| 02. Linker | `dentbird-linker-app` 외 | ✅ done |
| 03. MFE | `dentbird-front-module-monorepo`, `dentbird-console-client` | ⬜ 발굴 |
| 04. Account | `dentbird-account-client` | ⬜ 발굴 |
| 05. Batch | `workspace/dentbird-batch-client`, `dentbird-solutions/apps/batch` | ✅ done |
| 06. Landing | `landing-page-client`, `landing-page-server` | ✅ done |
| 07. Design System | `imago-design-system` | ⬜ 발굴 |
| 99. Misc | Datadog 스터디·AI 도구 운영 등 카드 외 | ⬜ 발굴 |

**완료 기준**: 4시그널 섹션이 채워지고, 본문에 박을 `[측정필요]`/`[기억]` 항목은 별도 확인 완료.

---

## Phase B — 기준 정비 ✅ 완료

| 항목 | 내용 | Status |
|------|------|--------|
| WRITING-GUIDE 포지셔닝 섹션 | "우산 + 4축" 개념 + 포지셔닝 1안 명시 (검증 대상) | ✅ §0-1 |
| EVALUATION-RUBRIC.md 신규 | 평가 서브에이전트 채점표·통과 기준 (포지셔닝 선명도/첫줄 임팩트/정량 실측/트레이드오프/6초 매력/발명 없음). **무한루프 방지 위해 통과 임계치 명문화** | ✅ 8항목+임계치 |

---

## Phase C — 새 이력서 생성

findings를 입력으로, **포지셔닝(1안) 렌즈 + [WRITING-GUIDE.md](./WRITING-GUIDE.md) 기준**으로 백지 재작성.

- `interviews/0X-*.md` 4축 인터뷰 **병행** (면접 답변 스크립트 + 카드 서사 깊이 확보)
- `career-description.md` 먼저(상세) → `resume.md`(압축·선별·정렬, WRITING-GUIDE §1-1)
- 기존 양식 답습 금지. 첫 줄 임팩트·문제→해결→임팩트·트레이드오프 서사 필수.

### 카드별 인터뷰 깊이 가이드 (기존 보존)

- **Solutions ★**: 모노레포 통합의 진짜 동기 / Playwright Page Object 93%·39%의 결정 근거 / Claude CLI 변경 감지 효과 측정 / Datadog RUM 전사 적용 경위 / Facade 토큰 200→33줄 Race Condition 맥락
- **Linker ★**: LNA Custom Protocol 20여 방안 의사결정 / 16개 CAM 검증 방식 / 1개월 113커밋 페이스
- **MFE ★**: iframe 4~5개 운영의 진짜 비용 / Module Federation 대안 검토 / FSD 도입 후 변화 / 지속성·재설계 계획
- **Account / DS / Misc**: 가벼운 4축 (결정 서사 위주)

---

## Phase D — 평가-개선 루프

- **평가 서브에이전트**: `EVALUATION-RUBRIC.md` + `WRITING-GUIDE.md` 기준으로 **비판적 채점** → `통과`/`미통과` + 구체 피드백
- 메인(생성자)이 피드백 반영 → 재평가 → **통과까지 반복**
- **통과 = 룹브릭 전 항목 합격 + 치명결함 0**
- **포지셔닝 1안도 여기서 검증·조정** (더 강한 포지셔닝이 보이면 교체)

---

## Phase E — 포맷·배포 (선택)

콘텐츠·평가 통과 후 진입.

| 항목 | Status |
|------|--------|
| Pages 배포 검증 | ⬜ |
| **JD별 이력서 버전 분기** (공고마다 다른 빌드 산출물 — WRITING-GUIDE §13 매핑) | ⬜ |
| PDF 자동 빌드 / `resume-style.css` 다듬기 | ⬜ |
| 공개 시 개인정보 분리 | ⬜ |

---

## 진행 규칙 요약

1. **A → B → C → D → E 순서**. 사용자가 점프 지시하면 점프 가능.
2. 각 Phase 완료 시 이 PLAN 상단 **Current Phase** 갱신.
3. **사실 발굴은** [findings/README.md](./findings/README.md) 4시그널. **인터뷰는** [interviews/README.md](./interviews/README.md) 4축.
4. **본문 작성·재작성은** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 기준 + 포지셔닝(1안) 렌즈. **기존 양식 답습 금지.**
5. **평가는** `EVALUATION-RUBRIC.md` 기준 비판적으로. 통과까지 개선.
6. **새 사실/수치를 박을 때마다** "사용자가 답했나, 내가 만들었나" 자체 검증. 출처 없으면 못 박음.
7. 한 PR = 한 카드 또는 한 Phase 정리 묶음. 변경 전후 보여주고 사용자 검토.

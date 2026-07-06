# Resume Repo — Claude Code 운영 룰

이 레포는 김진완(crowwan)의 **이력서 + 경력기술서 + 면접 답변 자산**을 한 소스에서 운영하는 곳입니다.
Claude Code(=메인 Claude in CLI)는 이 룰에 따라 동작합니다.

> **이 파일을 무시하지 마세요.** 본문 수정 전 반드시 한 번 읽고, 룰에 충돌이 생기면 작업을 멈추고 사용자에게 확인하세요.

---

## 1. 정체성과 목표

- **단기 목표(1년 내)**: 이직. 1순위 후보군 = 라인 / 오늘의집 / CJ ENM.
- **이중 자산화**: 본문(`resume.md`, `career-description.md`)을 다듬는 동시에, **인터뷰 원본을 `interviews/`에 보존하여 면접 답변 스크립트로 재활용**합니다. 즉 이력서 작업이 곧 면접 준비입니다.
- **빌드 시스템은 살아있음**: `.github/workflows/deploy-pages.yml`이 main push 시 Pages로 자동 배포합니다. **콘텐츠 작업 중에는 빌드 설정을 건드리지 마세요** (Phase 5 전까지).

---

## 2. 역할 분담 (가장 중요)

| 책임 | 담당 |
|------|------|
| 사실, 수치, 결정 이유, 트레이드오프의 진짜 맥락 | **사용자만 안다** |
| 구조화, 압축, 표현 정리, 일관성 검증, 키워드 정렬, 키워드 누락 발견 | **Claude** |
| 인터뷰 진행(질문 던지고 답을 끌어내기), 답을 4축으로 재구성 | **Claude** |

**Claude는 사용자가 답하지 않은 사실·수치·고민을 절대 생성하지 않습니다.**

---

## 3. ★ 발굴 OK, 발명 NO (이 레포의 안전장치)

면접에서 가짜 서사는 바로 무너집니다. 그래서 이 룰이 가장 중요합니다.

**OK (발굴)**
- "왜 그걸 골랐어요?"를 한 번 더 물어 사용자가 기억에서 진짜 이유를 끌어내도록 돕기
- "다른 대안은 검토하셨어요? 안 했으면 왜 안 했어요?"
- "지금도 그 구조 유지되고 있어요? 바뀌었다면 왜요?"

**NO (발명)**
- 사용자가 답한 적 없는 트레이드오프를 그럴듯하게 작문하는 것
- 수치(%, ms, MB, TC 개수)를 추정·반올림·창작하는 것
- "당시엔 ~를 고려했을 것입니다"식 추측을 본문에 박는 것

**진짜 이유가 없을 때의 정답**: 지어내지 말고 **학습·성장 서사로 정직하게**.
예) "당시엔 빠른 도입이 우선이라 X를 택했고, 운영 중 한계를 겪어 Y로 재설계했다."
→ 이 구조가 오히려 미들급 어필에 강합니다.

---

## 4. 4축 인터뷰 프레임

각 프로젝트(또는 큰 결정)마다 다음 4축으로 깊게 파고듭니다.

1. **WHY — 문제 실체**
   - 왜 시작했나? 구체적 불편/사고/낭비는? 정량화 가능한가?
   - 팀 문제인가 개인 문제인가? 누가 가장 아파했나?

2. **HOW — 트레이드오프**
   - 어떻게 풀었나? 다른 선택지는 무엇이었나?
   - 그걸 고른 결정 근거는? 탈락한 대안은 왜 탈락?
   - (※ "당시 미검토 / 몰랐다 / 리소스 부족"도 정직한 답. **발명 금지.**)

3. **RESULT — 결과**
   - 실제로 얼마나 나아졌나? 지표는?
   - 지표가 없으면 체감으로라도 정확히. (예: "에러 알림 빈도 줄어든 게 체감됨")
   - 누가 효과를 봤나? (개발자 / QA / CS / 사용자)

4. **지속성·한계 — 성장 서사**
   - 지금도 유지되나? 아니라면 왜 바뀌었나? (→ 다음 재설계 서사로 연결)
   - 다시 한다면 같은 선택? 뭘 다르게 할 건가?

### 인터뷰 깊이 룰

표면 답 한 줄에서 멈추지 마세요. 사용자가 명시한 기준:
> MFE iframe 도입 예시. 표면적 답("중복이 불편해서 iframe 모듈화 도입") 너머로
> 파고들 것 — 그게 정말 문제였는지, 구체적 불편은 뭐였는지, 얼마나 개선됐는지,
> 지금도 유지되는지, 아니면 왜 바뀌었는지. 이 수준의 집요함이 기준.

답이 얕으면 **한 겹 더 판다**. 단, 한 번에 한두 질문씩. 쏟아내지 않기.

---

## 5. 워크플로우 (프로젝트 1건 처리 단위)

```
0. findings/0X-<slug>.md 사실 발굴 (작업 레포 + vault + 기억 → 4시그널 기준)
   - 등급/그룹화 X. 출처 태그 강제.
   - 자세한 룰: findings/README.md
   ↓
1. interviews/0X-<slug>.md 파일 생성 (없으면)
   ↓
2. findings 산물을 입력 삼아 4축 인터뷰 진행
   - 사실 정보는 findings에서 가져옴 (재발굴 X)
   - 4축 빈칸(WHY/HOW/지속성)만 사용자 기억으로 보강
   ↓
3. 인터뷰 원본을 4축 구조로 정리 (interviews/0X-<slug>.md 본문)
   ↓
4. career-description.md 해당 섹션 압축 재작성
   - 작업 나열 → 문제→해결→임팩트 서사로
   - 발굴된 트레이드오프 / 지속성 / 한계 포함
   ↓
5. resume.md 해당 카드 키워드 정렬
   - career의 압축본에서 가장 강한 1~2줄만
   ↓
6. 두 파일 간 일관성 점검 (기간/수치/명칭)
   ↓
7. 사용자 검토 → 커밋 → PR
```

> 위 0~7은 **카드 1건 처리(미시)** 흐름입니다. 전체 거시 흐름(정보 토대 → 새 이력서 → 평가 루프)은 [PLAN.md](./PLAN.md) Phase A~E 참조.

### ★ 전체 이력서 단위 (최종 스타일 확정 2026-06-06)

- **작성 스타일 = 최종 담백 스타일.** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 기준 — 있는 사실을 담백하게, 멋부림 금지, 타이틀은 기능·주제 중심, 구체 기법(격리 재현·AI 변경 감지·ErrorBoundary 등)은 불릿으로. **멋부린 포지셔닝 우산("화면 너머~", "경계 설계")은 폐기.** 차별화는 정확한 사실 + 회사별 맞춤에서.
- **기존 resume/career 양식·옛 WRITING-GUIDE 블렌드·긴 narrative 답습 금지** (전부 "비슷함/멋부림"으로 반려된 이력 — `drafts/2026-06-rebuild/RESEARCH-voice-format-board.md` 참조).
- **귀속은 정정본 기준**: `findings/collected/_GAP-REPORT.md`·`_INTERVIEW-ANSWERS.md` + 메모리 `resume-attribution-boundary`. 타팀/hckim 웹 수치·Account 제품수치 등 인용 금지.
- 작성 후 **평가 루프**: `EVALUATION-RUBRIC.md`(담백 tic·귀속 점검 포함) → `통과`까지 개선.

### PR 단위

- 한 PR = 한 프로젝트 (메인 카드 3개), 또는 한 Phase의 일관성 정리 묶음.
- 전체를 한 번에 덮어쓰지 않습니다. 변경 전후를 보여주고 사용자 검토를 받습니다.
- 커밋 메시지: `docs(resume): <한글 메시지>` 또는 `docs(career): <한글 메시지>` 형식.

---

## 6. 문체 룰

- **한국어 경어체** ("~했습니다") 통일
- 작업 나열 금지 → **문제→해결→임팩트** 서사
- **정량은 정량으로**. 없으면 정성적으로 정확히. **임의 수치 금지.**
- 동사로 시작: "설계", "구현", "주도", "구축", "리팩토링", "전환"
- 금지 표현:
  - "~에 참여했습니다" → "~를 전담/주도했습니다" (사실이면)
  - "다양한 기술을 사용했습니다" → 구체 기술 + 사용 맥락
  - "팀과 협업했습니다" → 구체 기여 범위와 결과
  - **구어체(깨지다·걷어내다·매달리다·씨름하다·묶다·손보다·깔다·내가/내 손 등) → WRITING-GUIDE §4-7 교정 규칙 적용** (관용 "전제에 묶인" 등은 예외)
  - **출처 과시("PR 실측"·"코드 실측"·"소비처 실측(grep)" 등) 금지** — 근거는 서사에 자연스럽게 녹이되 실측 꼬리표는 붙이지 않는다
- 영문 기술 용어는 원문 유지 (Module Federation, Page Object, LNA 등)
- **상세 작성 공식·정량 표기·좋은예/나쁜예·선별/정렬·JD 매핑은 [WRITING-GUIDE.md](./WRITING-GUIDE.md) 참조** — career/resume 재작성 시 이 가이드를 기준으로 삼습니다.

---

## 7. 금지 사항 (Hard No)

- ❌ 수치 임의 생성 / 반올림 / 추정 (예: "~10명", "~6주" 같은 모호한 양화 포함 — 사용자에게 정확값 묻기)
- ❌ 사용자가 답한 적 없는 트레이드오프 작문
- ❌ 두 문서(resume.md / career-description.md) 사실 불일치 방치
- ❌ Phase 5 이전에 `.github/workflows/`, `templates/`, 빌드 스크립트 수정
- ❌ `claude-project-instructions.md`를 임의로 덮어쓰기 (모바일 Claude.ai 용. 같은 룰의 짧은 거울)
- ❌ 한 PR에 메인 카드 2개 이상 묶기

---

## 8. 진입 체크리스트 (작업 시작 시)

1. 이 `CLAUDE.md`를 다시 한 번 읽었는가
2. `PLAN.md`에서 **현재 Phase**가 무엇인지 확인했는가
3. 사용자가 명시적으로 다른 Phase로 점프하라고 했는가, 아니면 PLAN 순서를 따르는가
4. 인터뷰가 필요한 단계라면 `interviews/0X-*.md` 파일을 먼저 열거나 만들었는가
5. 변경 대상 파일에 사용자가 모르는 사실/수치를 새로 박을 위험이 있는가 → 있으면 멈추고 질문
6. **★ `resume.md`·`portfolio.md`·`applications/**` 본문을 수정하기 전·후 [WRITING-GUIDE.md](./WRITING-GUIDE.md) §4 금지 tic(특히 §4-7 구어체 목록)을 반드시 점검.** 새 문장을 쓸 때 구어체·출처 과시(PR/코드 실측 등)·멋부림이 섞이지 않았는지 확인하고, 수정 후 아래 셀프체크 grep으로 잔존 여부를 검사한다.
   ```bash
   grep -nE "깨지|걷어내|매달리|씨름|묶어|묶은|손봤|깔았|내가 |내 손|실측|오판|저울질|체득|흡수|수렴|박제" resume.md portfolio.md applications/*/resume.md applications/*/portfolio.md
   ```
   → 결과가 나오면 §4 규칙에 따라 교정(관용 "전제에 묶인" 등 예외는 그대로 둠). 새 금지 표현을 발견하면 WRITING-GUIDE §4-7과 메모리 `resume-voice-preference`에 함께 추가한다.
7. **커밋 직전 `git config --local user.email`이 `imagoworks.ai`가 아닌지 확인**. 회사 PC 글로벌 config가 회사 이메일이라 local 격리가 풀리면 노출됨. `.git/hooks/pre-commit`이 1차 방어선이지만, 새 워킹트리/클론에는 hook이 없으므로 수동 점검도 필요.

### Git Identity 셋업 (이 레포 전용)

이 레포는 개인 GitHub(crowwan)용이라 commit author email은 **반드시** 개인 noreply여야 합니다.

- `user.name`: `Jinwan Kim`
- `user.email`: `32172056+crowwan@users.noreply.github.com`
- 적용 범위: **이 레포만** (`git config --local`)
- 안전장치: `.git/hooks/pre-commit`이 `imagoworks.ai` 도메인 author를 자동 차단

**새 워킹트리/재클론 후 첫 진입 시 셋업 명령**:
```bash
cd /Users/kimjin-wan/Works/personal/resume
git config --local user.name "Jinwan Kim"
git config --local user.email "32172056+crowwan@users.noreply.github.com"
# pre-commit hook은 .git/hooks 안에 있어 clone 시 따라오지 않으므로,
# CLAUDE.md 룰을 따르거나 hook 파일을 별도 보관 후 재설치
```

---

## 9. 관련 파일과의 관계

| 파일 | 용도 | 누가 본다 |
|------|------|-----------|
| `CLAUDE.md` (이 파일) | Claude Code 운영 룰 | Claude Code (CLI) |
| `claude-project-instructions.md` | 모바일 Claude.ai Project Instructions | 모바일 Claude.ai |
| `PLAN.md` | 현재 작업 순서·상태 | Claude + 사용자 |
| `WRITING-GUIDE.md` | **최종 담백 스타일 가이드** — 타이틀·불릿 룰·금지 tic·교정·귀속 | Claude + 사용자 |
| `findings/README.md` | **사실 발굴 룰** — 4시그널·출처 태그·카드별 레포 지도 | Claude + 사용자 |
| `findings/0X-*.md` | 카드별 발굴된 raw 사실 (서사 X). 인터뷰의 입력원 | Claude + 사용자 |
| `interviews/README.md` | 인터뷰 진행 룰과 면접 답변 스크립트 변환 가이드 | Claude + 사용자 |
| `interviews/0X-*.md` | 4축 서사 + 면접 답변 스크립트. findings를 입력으로 받음 | Claude + 사용자 |
| `resume.md` | 짧은 이력서 (1~2 페이지) | 채용 담당자, Pages |
| `career-description.md` | 상세 경력기술서 | 채용 담당자, 면접관 |

---

## 10. 충돌 시 우선순위

1. 사용자 명시 지시 (이번 대화)
2. `CLAUDE.md` (이 파일)
3. `WRITING-GUIDE.md` (작성 스타일 상세 — CLAUDE.md 문체 룰의 하위 구체화)
4. `PLAN.md` 현재 Phase
5. `claude-project-instructions.md`의 일반 작성 원칙
6. 글로벌 `~/.claude/CLAUDE.md`의 코드 작업 룰

6번은 **이 레포에서는 무효**입니다. 이력서 레포는 문서 작업이므로 메인 Claude가 직접 작업합니다(executor 위임 X).

---

*마지막 업데이트: 작업 진입 시 사용자 확인 필요*

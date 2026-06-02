# Findings — 발굴된 사실 보관소

이력서/경력기술서/인터뷰 작성에 들어가기 전에, 실제 작업 레포·vault·기억에서 **의미있는 사실만** 발굴해 저장하는 곳입니다.

> 여기엔 서사 없음. 서사는 `interviews/`에서. 형식화는 본문에서.

---

## 3단계 흐름

```
findings/0X-*.md          (raw 사실 — 등급/그룹화 X, 출처 강제)
    ↓
interviews/0X-*.md        (4축 서사 + 면접 답변 스크립트, 이력서와 병행)
    ↓
resume.md / career-description.md   (포지셔닝 렌즈 + WRITING-GUIDE 기준 작성)
    ↓
평가 서브에이전트 루프     (EVALUATION-RUBRIC 비판적 채점 → 통과까지)
```

findings는 그 윗단계의 **사실 입력원**입니다. 단계를 건너뛰면 본문에 가짜 서사가 박힙니다. (전체 흐름은 [PLAN.md](../PLAN.md) Phase A~E)

---

## "의미있는 정보" 4가지 시그널

하나라도 걸리면 수집. 등급/그룹화는 여기서 하지 않습니다 (`interviews/` 단계에서).

### 1. 의사결정 흔적
- 왜 그걸 골랐나, 다른 안 검토 흔적
- 출처 예: PR 본문, `vault/projects/*/decisions/*`, POC 문서, 코드 리뷰 코멘트

### 2. 측정 가능한 변화
- Before → After 비교 가능한 수치
- 출처 예: 빌드 산출물, 모니터링 대시보드, 측정 스크립트, `vault/projects/*/learnings/*`

### 3. 후속 영향
- 다른 작업의 발판이 됐거나 재설계로 이어진 것
- 출처 예: 후속 PR의 "왜 바뀌었나" 본문, `vault/projects/*/decisions/*` 중 재설계 키워드

### 4. 운영 패턴 (자잘한 작업 묶을 단서)
- 같은 영역을 반복적으로 책임진 흔적
- 자잘한 버그/기능도 여기 4번에 걸리면 자동 수집
- 출처 예: git log 같은 모듈 반복 커밋, `vault/projects/*/bugs/*`

---

## 배제 기준 (수집 X)

- 단순 누적 커밋 수 / PR 수 / LOC — 페이스(예: 1주 53커밋)가 의미를 가질 때만 예외
- 단발성 운영 작업 중 4번 시그널에도 안 걸리는 것 (일회성 오타 수정 등)
- 사용자가 기억 못 하고 출처도 못 찾는 항목 → 차라리 비워두기

---

## 출처 표기 룰

발굴된 모든 항목은 **출처 태그**를 함께 남깁니다. 출처 없는 사실은 본문에 못 박습니다.

| 출처 태그 | 의미 |
|-----------|------|
| `[vault: <경로>]` | work-vault에서 발굴 |
| `[git: <레포>@<sha 또는 tag>]` | git log/tag에서 발굴 |
| `[PR: <레포>#<번호>]` | PR 본문/리뷰에서 발굴 |
| `[code: <파일경로>]` | 현재 코드 상태에서 확인 |
| `[기억]` | 사용자 기억만. 추가 확인 권장 |
| `[측정필요]` | 수치 출처 없음. 측정/확인 후 다시 채울 항목 |

예시:
```
- Electron 인스톨러 854→78MB [기억] [측정필요]
- Chrome PNA 대응 결정 [vault: projects/dentbird-solutions/decisions/2025-XX-pna.md]
- Linker v1.0.0~v1.0.3 4개 메이저 [git: dentbird-linker-app, tag --list]
```

---

## 카드별 발굴 레포 지도

| 카드 | 작업 레포 (`~/Works/devops/`) | vault 자산 |
|------|------|------|
| 01. Solutions | `dentbird-solutions`, `dentbird-cloud-client-monorepo` 외 | ★ 풍부 (`projects/dentbird-solutions/` log/learnings/decisions) |
| 02. Linker | `dentbird-linker-app` | TBD (vault 별도 폴더 확인 필요) |
| 03. MFE | `dentbird-front-module-monorepo`, `dentbird-console-client` | TBD |
| 04. Account | `dentbird-account-client` | TBD |
| 05. Batch | 확인 필요 (`crown-monorepo`?) | TBD |
| 06. Landing | `landing-page-client`, `landing-page-server` | TBD |
| 07. Design System | `imago-design-system` | TBD |
| 99. Misc | — | Datadog 스터디 등 카드 외 |

---

## 파일 골격

각 카드 파일은 다음 골격을 따릅니다.

```markdown
# 0X. <카드명> — 발굴된 사실

> 수집 기준: README.md 참고
> Status: empty | partial | done

## 1. 의사결정 흔적
- 

## 2. 측정 가능한 변화
- 

## 3. 후속 영향
- 

## 4. 운영 패턴
- 

## 미해결 (출처 확인 필요)
- 
```

---

## 다음 단계로 넘어가는 시그널

- 4가지 시그널 섹션이 어느 정도 채워졌고
- `[측정필요]` / `[기억]` 항목 중 **본문에 꼭 필요한 것**은 별도 확인 완료
- → 해당 카드의 `interviews/0X-*.md` 4축 인터뷰 시작

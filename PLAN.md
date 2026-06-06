# Resume Repo — 작업 계획서

> 1년 내 이직(라인·오늘의집·채널톡·토스플레이스) 기준. **1차 자료 발굴 → 귀속 정정 → 담백 재작성 → 회사별 변형**으로 운영.
>
> 운영 룰: [CLAUDE.md](./CLAUDE.md) · 작성 기준: [WRITING-GUIDE.md](./WRITING-GUIDE.md) · 평가: [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md) · 발굴 룰: [findings/README.md](./findings/README.md)

---

## 현재 상태 (2026-06-06)

**Phase: 최종 빌드 검토 중.** 대규모 발굴로 기존 이력서의 오귀속(타팀/hckim 웹 인프라 수치)·누락(3D SRGB·관측 표준·Subtree)을 정정하고, 여러 라운드의 문체 calibration 끝에 **레퍼런스식 담백 스타일**로 확정해 마스터 + 4사 변형을 재작성했습니다.

- 정본 산출 위치: `drafts/2026-06-rebuild/final/` (career-description.md · resume.md · variants/{라인,오늘의집,채널톡,토스플레이스})
- 발굴 raw 사실: `findings/collected/` (+ `_GAP-REPORT.md`, `_INTERVIEW-ANSWERS.md`)
- 보이스 리서치: `drafts/2026-06-rebuild/RESEARCH-voice-format-board.md`

---

## 실제 진행한 파이프라인 (이번 재작성)

```
1. 대규모 자료 수집   본인(jwkim) 커밋·PR·Confluence·Jira·vault·S3 전수 발굴 → findings/collected/
      ↓
2. 귀속 정정         git/Confluence author 교차검증 → 오귀속 제거·누락 강점 격상 (_GAP-REPORT)
      ↓
3. 발명 방지 인터뷰   사용자만 아는 사실·수치·기여 확인 (_INTERVIEW-ANSWERS)
      ↓
4. 보이스 리서치      한국 IT 컨벤션 밖 사례 수집 → 담백 스타일 확정 (RESEARCH-voice-format-board)
      ↓
5. 담백 재작성        최종 스타일로 마스터 8카드 + career/resume (final/)
      ↓
6. 회사별 변형        4사 렌즈로 재정렬 + 담백한 회사-맞춤 도입부 (final/variants)
      ↓
7. 회사별 평가        RUBRIC + 귀속·tic 점검 (통과까지)
```

핵심 원칙은 그대로: 사실·수치는 **발굴 OK / 발명 NO** (CLAUDE.md §3). 본인 기여 경계는 메모리 `resume-attribution-boundary` 참조.

---

## 남은 작업

| 단계 | 내용 | Status |
|------|------|--------|
| 정본 검토 | 사용자가 `final/` 검토·미세 조정 | 🔵 진행 |
| 정본 승격 | `final/` → 루트 `resume.md`·`career-description.md` + `applications/` 4종 교체(기존 백업) | ⬜ |
| 포맷·배포 | HTML/Pages 빌드 검증, PDF, `resume-style.css` | ⬜ |
| 포트폴리오 분리 | 서사·스토리텔링은 `portfolio.md`로 분리(담백 이력서와 역할 구분) | ⬜ 향후 |
| 커밋·PR | 한 묶음씩 변경 전후 보여주고 커밋 (개인 noreply 이메일 확인 — CLAUDE.md §8) | ⬜ |

---

## 진행 규칙 요약

1. **사실 발굴은** [findings/README.md](./findings/README.md) 4시그널 + 출처 태그. **귀속은** git/Confluence author 교차검증.
2. **작성은** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 최종 담백 스타일. **평가는** [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md).
3. 새 사실/수치를 박을 때마다 "사용자가 답했나, 내가 만들었나" 자체 검증. 출처 없으면 못 박음.
4. 커밋 직전 `git config --local user.email`이 `imagoworks.ai`가 아닌지 확인.

# Resume Repo — 작업 계획서

> 1년 내 이직(라인·오늘의집·채널톡·토스플레이스) 기준. **1차 자료 발굴 → 귀속 정정 → 담백 재작성 → 회사별 변형**으로 운영.
>
> 운영 룰: [CLAUDE.md](./CLAUDE.md) · 작성 기준: [WRITING-GUIDE.md](./WRITING-GUIDE.md) · 평가: [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md) · 발굴 룰: [findings/README.md](./findings/README.md)

---

## 현재 상태 (2026-06-08)

**Phase: 문서 아키텍처 재편 중.** 이력서는 담백(업무 중심), 서사는 포트폴리오로 역할을 분리합니다. 경력기술서는 주 제출물에서 빼고 archive로 보존합니다.

**목표 아키텍처**
- `resume.md` + `applications/*/resume.md` → 담백 이력서 (범용 + JD 맞춤)
- `portfolio.md` + `applications/*/portfolio.md` → 서사 케이스 스터디 (범용 + JD 큐레이션)
- `archive/career-description.md` → 보존 (빌드·index 링크 제외, 필요한 회사에만 수동 제출)

원칙: resume/career는 "무엇을 했나(담백)", portfolio는 "왜·어떻게·무엇을 배웠나(서사)". JD 변형은 **내용 재작성이 아니라 Case 선별·순서·강조(큐레이션)**.

- 발굴 raw 사실: `findings/collected/` (+ `_GAP-REPORT.md`, `_INTERVIEW-ANSWERS.md`)
- 귀속 경계: 메모리 `resume-attribution-boundary`

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
| resume 마스터 담백화 | 6카드 업무 중심 재작성, 서사 불릿 제거 | ✅ |
| career 아카이브 | `archive/` 이동 + 빌드·index 링크 제외 | ✅ |
| resume 변형 담백화 | `applications/*/resume.md` 4사를 마스터 담백 톤에 일치 | ⬜ |
| portfolio 범용 완성 | 현 4 Case → 빌드·3D·결제 추가, resume 6카드와 대응되는 Case 세트 | ⬜ |
| portfolio JD 큐레이션 | `applications/*/portfolio.md` 4사 Case 선별·순서·강조 | ⬜ |
| 빌드·배포·커밋 | HTML 빌드 검증, 개인 noreply 확인 후 커밋 (CLAUDE.md §8) | ⬜ |

---

## 진행 규칙 요약

1. **사실 발굴은** [findings/README.md](./findings/README.md) 4시그널 + 출처 태그. **귀속은** git/Confluence author 교차검증.
2. **작성은** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 최종 담백 스타일. **평가는** [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md).
3. 새 사실/수치를 박을 때마다 "사용자가 답했나, 내가 만들었나" 자체 검증. 출처 없으면 못 박음.
4. 커밋 직전 `git config --local user.email`이 `imagoworks.ai`가 아닌지 확인.

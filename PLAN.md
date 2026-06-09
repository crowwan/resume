# Resume Repo — 작업 계획서

> 1년 내 이직(라인·오늘의집·채널톡·토스플레이스) 기준. **1차 자료 발굴 → 귀속 정정 → 담백 재작성 → 회사별 변형**으로 운영.
>
> 운영 룰: [CLAUDE.md](./CLAUDE.md) · 작성 기준: [WRITING-GUIDE.md](./WRITING-GUIDE.md) · 평가: [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md) · 발굴 룰: [findings/README.md](./findings/README.md)

---

## 현재 상태 (2026-06-09)

**Phase: 이력서 축 완료 · 포트폴리오 초안 완료 · 발굴 백로그 확보. 다음 = 백로그 검토 → 케이스 승격.**

**문서 아키텍처 (2축, 확정)**
- `resume.md` + `applications/*/resume.md` → 담백 이력서 (범용 + JD 4사) — ✅ 담백화 완료
- `portfolio.md` → 서사 케이스 스터디 11개 (PDF 형식) — ✅ 초안 완료
- `archive/career-description.md` → 보존 (빌드·index 제외) — ✅
- `applications/*/portfolio.md` → JD 큐레이션 — ⬜ 미착수

원칙: resume는 "무엇을 했나(담백)", portfolio는 "왜·어떻게·무엇을 배웠나(서사)". JD 변형은 **내용 재작성이 아니라 Case 선별·순서·강조(큐레이션)**. 카드 추상화 = "판단 보이는가 + 오귀속 안 하는가"(메모리 `resume-architecture-and-card-abstraction`).

**핵심 산출물 위치**
- 이력서: `resume.md`(루트, Pages 자동배포) + `applications/{라인,오늘의집,채널톡,토스플레이스}/`
- 포트폴리오: `portfolio.md`(루트) · 전용 스타일 `templates/portfolio-style.css` · 다이어그램 `templates/mermaid.html`
- ★ **발굴 백로그**: `findings/portfolio-case-backlog.md` — 2025-09~2026-06 전 구간, 신규 케이스 후보 ~18개 + 기존 11케이스 보강 + 귀속주의
- 귀속 경계 메모리: `resume-attribution-boundary` · 코드사인 미도입: `resume-codesign-build-integration-not-adopted` · 2축/추상화 원칙: `resume-architecture-and-card-abstraction`
- 발굴 raw: `findings/collected/` (+ `_GAP-REPORT.md`, `_INTERVIEW-ANSWERS.md`)

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

## 이번 세션 (2026-06-08~09) 완료

| 작업 | 상태 |
|------|------|
| resume 마스터 6카드 담백화 (판단 중심 + 사실 정정: 크론잡·익명화·코드사인 미도입·공존X 등) | ✅ |
| career-description → `archive/` (빌드·index 제외) | ✅ |
| resume 변형 4사 담백화 (오늘의집·채널톡·토스·라인) | ✅ |
| portfolio 11케이스 전면 재작성 (PDF 형식, reviewer 2회 반영) | ✅ |
| portfolio 전용 스타일 `portfolio-style.css` + mermaid 테마 | ✅ |
| 메모리 2건 박제 (코드사인 미도입 · 2축/추상화 원칙) | ✅ |
| 대규모 발굴 → `findings/portfolio-case-backlog.md` (전 구간, ~18 신규후보) | ✅ |

## 다음 세션 할 일 (우선순위)

| # | 작업 | 메모 |
|---|------|------|
| 1 | **백로그 검토 → 케이스 승격 선별** | `findings/portfolio-case-backlog.md`에서 고름. 추천: TC-Verify(Case5 전신)·Mock Server·Feature Flag render-props·세션인증 cross-app 공통화·리팩토링 비용편익 사다리 |
| 2 | **승격 케이스 git 검증** | 선택 케이스만 `git -C ~/Works/devops/dentbird-solutions show/diff` + PR 본문으로 **정량 실측·코드 발췌·author 귀속** 확정 (발명 금지) |
| 3 | **이미지/코드 플레이스홀더 → 실물** | portfolio의 🖼️ = 사용자 스크린샷, 💻 = 회사레포 코드 발췌 |
| 4 | **portfolio JD 큐레이션** | `applications/*/portfolio.md` 4사 — Case 선별·순서·강조 (내용 재작성 X) |
| 5 | 다이어그램 스타일 추가 다듬기 | 사용자 "나중에" — mermaid 색/모양 or 핵심 1~2개 SVG 교체 |

## 미해결 확인사항 (사용자 답 필요 — 발명 방지)

- **Case 5 "K8s 대신 EC2" → "K8s 구축 후 EC2 전환"** 정정 검토 (git D1-868→888로 확인, 백로그 H2)
- **blue-green 보류 근거** "환경·빌드 2배 비용"이 실제 판단인지 (career 출처, 발굴 노트엔 근거 없음)
- **CAM 수 12종 확정** 여부 (findings엔 12/16 혼재 — 현재 이력서·portfolio는 12종 통일)
- **Case 1 "핵심 경로에서 분리"** 가 실제 진행 방향인지

> 빌드: 루트 `resume.md`·`portfolio.md` 수정 시 `.github/workflows/deploy-pages.yml`이 Pages 자동배포. 커밋 전 `git config --local user.email`이 `imagoworks.ai` 아닌지 확인 (CLAUDE.md §8).

---

## 진행 규칙 요약

1. **사실 발굴은** [findings/README.md](./findings/README.md) 4시그널 + 출처 태그. **귀속은** git/Confluence author 교차검증.
2. **작성은** [WRITING-GUIDE.md](./WRITING-GUIDE.md) 최종 담백 스타일. **평가는** [EVALUATION-RUBRIC.md](./EVALUATION-RUBRIC.md).
3. 새 사실/수치를 박을 때마다 "사용자가 답했나, 내가 만들었나" 자체 검증. 출처 없으면 못 박음.
4. 커밋 직전 `git config --local user.email`이 `imagoworks.ai`가 아닌지 확인.

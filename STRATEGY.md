# 이력서 관리 전략

## 접근 채널 Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    GitHub (crowwan/resume)                   │
│                   Single Source of Truth                     │
│         resume.md / career-description.md                   │
└──────────┬──────────────┬──────────────┬────────────────────┘
           │              │              │
     ┌─────┴─────┐ ┌─────┴─────┐ ┌─────┴──────┐
     │  PC 작업   │ │ 모바일     │ │  자동화     │
     │           │ │           │ │            │
     │ Claude    │ │ Claude.ai │ │ GitHub     │
     │ Code      │ │ 앱        │ │ Actions    │
     │    +      │ │    +      │ │            │
     │ Claude    │ │ GitHub    │ │ PDF 빌드   │
     │ Desktop   │ │ 앱        │ │            │
     └───────────┘ └───────────┘ └────────────┘
```

---

## 채널 1: PC - Claude Code (메인 작업)

> 집중 편집, 대량 수정, 프로젝트 성과 추가 시 사용

### 사용 시나리오
- 새로운 프로젝트 성과를 이력서에 추가
- 경력기술서 대규모 수정
- 포지션별 맞춤 이력서 작성

### 워크플로우
```
1. Claude Code에서 resume 저장소 열기
   $ cd ~/Works/personal/resume

2. resume-ref 스킬 + 이력서 편집
   "이 프로젝트를 STAR 기법으로 정리해줘"
   "정량적 성과를 더 임팩트 있게 써줘"

3. 수정 완료 후 커밋 & push
   $ git add . && git commit && git push

4. GitHub Actions가 PDF 자동 생성
   → Artifacts에서 다운로드 가능
```

---

## 채널 2: PC - Claude Desktop + GitHub MCP (대화형)

> 대화하면서 이력서를 읽고/수정하고/커밋까지 한번에

### 초기 세팅

Claude Desktop 설정 파일에 GitHub MCP 추가:

**파일**: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<PAT>"
      }
    }
  }
}
```

GitHub PAT 필요 권한: `repo` (비공개 저장소 접근)

### 사용 시나리오
- "내 이력서 읽어서 프론트엔드 시니어 포지션에 맞게 수정해줘"
- "경력기술서에서 Electron 관련 내용만 강조해줘"
- Claude가 GitHub MCP로 직접 파일 읽기 → 수정 → 커밋 → push

### 워크플로우
```
1. Claude Desktop 열기
2. "crowwan/resume 저장소의 resume.md 읽어줘"
3. "핵심 역량 부분을 시니어 포지션에 맞게 수정해줘"
4. Claude가 MCP로 직접 커밋 & push
5. PDF 자동 생성
```

---

## 채널 3: 모바일 - Claude.ai 앱 + GitHub 앱

> 출퇴근, 외출 중 아이디어 반영

### 초기 세팅

1. **claude.ai** 접속 → Projects → "이력서 관리" 생성
2. **GitHub 저장소 연결**:
   - Project Settings → Integrations → GitHub
   - `crowwan/resume` 저장소 연결
   - `resume.md`, `career-description.md` 선택
   - → Knowledge로 자동 동기화됨
3. **Project Instructions**에 `claude-project-instructions.md` 내용 붙여넣기
4. 모바일 Claude 앱에서 해당 Project 접근 확인

### 사용 시나리오
- 면접 준비 중 특정 프로젝트 표현 다듬기
- JD 보면서 맞춤 수정 요청
- 갑자기 떠오른 성과 메모

### 워크플로우
```
[Claude 모바일 앱]
1. "이력서 관리" Project 열기
2. "토큰 관리 리팩토링 부분을 더 임팩트 있게 써줘"
3. Claude가 수정안 제시 (마크다운)
4. 수정안 복사

[GitHub 모바일 앱]
5. crowwan/resume → resume.md 열기
6. 편집 → 해당 부분 교체
7. 커밋 → PDF 자동 생성

[또는 나중에 PC에서 일괄 반영]
5. Claude 대화 내용 확인
6. Claude Code에서 반영 & 커밋
```

### Knowledge 동기화
- 저장소 내용이 바뀌면 Project에서 "Sync now" 클릭
- 항상 최신 이력서를 기반으로 대화 가능

---

## 시나리오별 추천 채널

| 시나리오 | 추천 채널 | 이유 |
|----------|----------|------|
| 새 프로젝트 성과 추가 | Claude Code | resume-ref 스킬 + 직접 편집 |
| 경력기술서 대규모 수정 | Claude Code | 대량 편집에 적합 |
| 포지션별 맞춤 이력서 | Claude Desktop + MCP | 대화하면서 수정+커밋 한번에 |
| 출퇴근 중 표현 다듬기 | Claude.ai 모바일 | 이동 중 접근 용이 |
| JD 기반 맞춤 수정 | Claude.ai 모바일 | JD 복사 → 대화로 매칭 |
| 면접 직전 리뷰 | Claude.ai 모바일 | 빠른 확인 |
| 주간 이력서 업데이트 | Claude Code | 정기적 반영 |
| 긴급 수정 (오타 등) | GitHub 모바일 앱 | 직접 편집 가장 빠름 |

---

## 포지션별 브랜치 전략

```
main                    ← 기본 이력서 (범용)
├── target/naver        ← 네이버 맞춤
├── target/kakao        ← 카카오 맞춤
├── target/toss         ← 토스 맞춤
└── target/startup-a    ← 스타트업 A 맞춤
```

### 포지션별 워크플로우
```
1. 타겟 회사 JD 확인
2. 브랜치 생성: git checkout -b target/company-name
3. Claude에 JD 공유: "이 JD에 맞게 이력서 수정해줘"
4. 수정 → 커밋 → PDF 생성
5. 면접 종료 후 좋은 표현은 main에 머지
```

---

## 정기 관리 루틴

### 주간 (금요일)
- 이번 주 커밋/PR 중 이력서에 반영할 성과 확인
- resume-ref 스킬로 문장 변환
- career-description.md 업데이트

### 월간
- 전체 이력서 리뷰 (outdated 내용 정리)
- 정량적 수치 업데이트
- Claude.ai Knowledge "Sync now"

### 이직 준비 시
- 타겟 회사별 브랜치 생성
- JD 기반 맞춤 수정
- PDF 빌드 → 제출

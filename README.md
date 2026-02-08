# Resume

이력서와 경력기술서를 마크다운으로 관리하는 저장소입니다.

## 파일 구조

```
resume.md               # 이력서
career-description.md   # 경력기술서
templates/
  resume-style.css      # PDF 변환용 스타일
.github/workflows/
  build-pdf.yml         # 마크다운 -> PDF 자동 빌드
```

## PDF 빌드

`main` 브랜치에 push하면 GitHub Actions가 자동으로 PDF를 생성합니다.

- Actions 탭 > 최근 워크플로우 > Artifacts에서 다운로드
- 태그 생성 시 Release에 PDF 자동 첨부

### 로컬 빌드 (선택)

```bash
# 의존성 설치
brew install pandoc
pip install weasyprint

# PDF 생성
mkdir -p output
pandoc resume.md -o output/resume.html --standalone --css=templates/resume-style.css
weasyprint output/resume.html output/resume.pdf
```

## 모바일 편집 워크플로우

1. **Claude 모바일 앱**에서 "이력서 관리" Project 열기
2. 수정할 내용 대화로 요청
3. **GitHub 모바일 앱**에서 수정 내용 커밋
4. Actions가 자동으로 최신 PDF 생성

## 포지션별 버전 관리

특정 회사/포지션에 맞춘 이력서가 필요하면 브랜치를 만들어 관리합니다.

```bash
git checkout -b target/company-name
# 해당 포지션에 맞게 이력서 수정 후 커밋
```

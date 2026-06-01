# 08. Electron 데스크톱 빌드·배포·자동업데이트 인프라 — 4축 인터뷰

> 인터뷰 일자: 2026-06-01
> Status: done (career 프로젝트 6 + resume 프로젝트 6 인프라 카드로 반영 완료, 2026-06-01)
> 입력: [findings/05-batch.md](../findings/05-batch.md) §1·§6 + [findings/02-linker.md](../findings/02-linker.md) (빌드/자동업데이트 공통)
> 카드 위상: **두 Electron 앱(Batch·Linker)에 걸친 횡단 인프라 카드** — 제품 기능이 아니라 빌드·배포·자동업데이트 파이프라인 자체가 주제

## 사실 정보 (Facts) — findings에서 확정

- **기간/범위**: 초기 Batch 자동 업데이트·배포 설계(2024, Bitbucket) → P004 빌드 파이프라인 재설계 → **GitHub Actions self-hosted 이관 주도(2026-04)**. Linker도 동일 인프라 위에서 운영.
- **역할**: **P002·P004 빌드 파이프라인 구축 + 초기 Batch 자동 업데이트·배포 전략 설계**. Azure Pipelines → GHA 이관 본인 주도(`dentbird-solutions #7689`).
- **규모(출처)**:
  - GHA 이관 PR: `#7689`(이관), `#7861`(macOS universal+자동업데이트 정상화), `#7929`(NAS 직접 업로드·artifact 제거), `#7884`(러너 안정화), `#8195`(Win version 전파 회귀 수정) `[git]`
  - 초기 Batch 자동 업데이트 Confluence 본인 작성 4건: `405373813` / `406850081` / `565282665` / `890798124` `[✅ 읽음 2026-06-01]`
- **주요 기술**: electron-builder, electron-updater, GitHub Actions(self-hosted runner: macOS ARM64 / Windows `solution-windows-builder`), Azure Pipelines(과거), Azure Storage→S3, NAS, `xcrun notarytool`, `signtool`, blue-green
- **특이 사실**:
  - ★ career 5번 "1인 개발"은 **오류** — Batch는 팀 개발, 본인이 최다 기여자(초기 batch jwkim 347커밋 / 2위 126) `[git]`
  - 빌드 시간 20분+ → 6분 내외는 **체감값** `[측정필요: CI 로그]` → 본문은 정성+체감 표현

---

## 1. WHY — 문제 실체

**Q1. 빌드·배포·자동 업데이트 인프라를 왜 갈아엎었나? 구체적으로 뭐가 아팠나?**

> findings 확정 (초기 Batch 빌드 방식의 문제):
> - 빌드 = **로컬 PC에서 직접 빌드 → Azure Storage 업로드**
> - 문제 1: **빌드 담당 PC를 못 쓰는 순간 난감** (속인성)
> - 문제 2: **빌드 방법 아는 사람이 담당자뿐** (지식 속인성)
> - 문제 3: 빌드/배포 요청이 담당자에게 몰려 **커뮤니케이션 비용** 발생
> - Windows는 **코드사인 USB를 인프라팀이 관리** → 매번 인프라팀 거쳐야 빌드 가능
> - → **초기엔 해결 못 함.** 팀 개편으로 다른 팀이 P002 개발하게 됨

**Q1-후속. 자동 업데이트 쪽 불편은? (배포 사고 위험)**

> findings 확정 + ✅ 사용자 정정(2026-06-01):
> - **별도 코드사인 시 checksum 불일치 (개발 중 학습한 점)**: 코드사인 없이 빌드한 아티팩트를 이후에 따로 코드사인하면 바이너리 checksum이 바뀌어 `latest.yml`의 sha512와 불일치 → 자동 업데이트 실패 (`565282665`)
>   - ★ **실제 배포는 코드사인을 빌드 과정에 포함**하므로 이 문제가 발생하지 않음. 즉 "해결한 기능"이 아니라 **코드사인을 빌드에 통합해야 한다는 근거**로 학습된 점
> - **QA/PROD가 같은 파일을 바라보면 사용자가 QA 버전을 받는 사고 위험** (`406850081`)

---

## 2. HOW — 트레이드오프

**Q2. 어떻게 풀었나? 단계별 결정과 탈락 대안은?**

> findings 확정 — **빌드 파이프라인 (P004에서 재설계):**
> 1. **Mac 먼저**: notarize 필요 파일을 **DevOps library에 추가**해 빌드 자동화
> 2. **Windows**: 코드사인 USB·인프라팀 의존 → **본인 Windows PC를 빌드머신**으로 파이프라인 구축
> 3. **DevOps(Azure) Mac 파이프라인**: Azure 클라우드 러너 → 느림(**20분+**)
> 4. **GitHub Actions 전환 (본인 주도, 2026-04, #7689)**: Azure Pipelines → **GHA + self-hosted 러너** 이관
>    - macOS: `runs-on: [self-hosted, macOS, ARM64]`, 영구 캐시 경로(`imago-builds`)로 BrokerServer 부하 회피, `xcrun notarytool`
>    - Windows: `runs-on: [self-hosted, Windows, solution-windows-builder]`, `signtool` 서명 검증, NAS 직접 업로드
>    - **Phase 0.3 ~ 4 단위로 체계적 진행**

> findings 확정 — **자동 업데이트:**
> - **electron-forge vs electron-builder 의사결정**: 커스터마이징 쉽고 자료 많은 **electron-builder 선택** (`405373813`)
> - **Custom Publisher 직접 구현(실험)**: electron-builder `BitbucketPublisher` 클래스를 분석해 **hostname 누락 버그 수정·timeout 120s→20분·Bearer 토큰 인증**까지 직접 패치 (issue #6192 기반)
>   - → 실제 적용은 클래스 대신 **업데이트용 yaml만 커스텀**하는 방향으로 단순화
>   - ★ **라이브러리 내부까지 파고든 깊이**
> - **QA/PROD 채널 분리**: `publish.channel` 조정으로 **채널별 latest.yml 분리** (`406850081`)
> - **checksum mismatch (학습)**: 별도 코드사인 시 checksum 불일치로 업데이트 실패 → 당시 sha512 재계산으로 임시 대응, 본질적으로는 코드사인을 빌드에 포함해 회피 (`565282665`)

> findings 확정 — **배포 경로:**
> - 저장소: Azure Storage → **S3** 전환
> - **QA 빠른 설치 경로 설계**: 빌드 → 빌드머신(self-hosted, NAS 마운트)이 **사내 NAS에 직접 업로드** → 인프라팀이 NAS→S3 → 자동 업데이트
>   - = QA팀이 자동 업데이트 안 기다리고 **NAS에서 최신 버전 바로 설치** 가능

**Q2-후속. 탈락/무산된 시도는?**

> - 초기 Bitbucket 파이프라인 빌드→즉시 자동 업데이트 시도 → **코드사인이 인프라팀 소관이라 무산** (→ 나중에 본인 빌드머신으로 해결)

---

## 3. RESULT — 결과

**Q3. 실제로 얼마나 나아졌나?**

> findings 확정:
> - **빌드 시간 20분+ → 6분 내외** (Azure 클라우드 러너 → self-hosted iMac) `[기억/체감]`
> - **빌드 속인성 제거**: 담당 PC·담당자 의존 → 파이프라인 자동화 (정성적)
> - **인프라팀 의존 제거**: 코드사인 USB·인프라팀 빌드 → 자체 빌드머신 (정성적)

**Q3-후속. 누가 효과를 봤나?**

> - QA팀: NAS 직접 업로드로 **자동 업데이트 안 기다리고 최신 버전 즉시 설치**
> - 팀 전체: 빌드/배포 요청 병목(담당자 몰림) 해소

---

## 4. 지속성·한계 — 성장 서사

**Q4. 지금도 유지되나? 진행 중 고민은?**

> findings 확정 — **진행 중 설계 고민 (S3 직접 업로드):**
> - 목표: **S3까지 파이프라인이 직접 업로드**
> - 막힌 이유: **S3 업로드 순간 자동 업데이트가 즉시 발동** → 검증 전 배포 위험
> - 현재 **blue-green 배포** 구성. green 검증 = 앱 실행 시 **특정 플래그 명령어**로 확인
> - 최종 고민: Electron 앱(**Batch·Linker**) 모두 blue-green 가능하게 **S3 구축** 검토 중

**Q4-후속. green 검증 자동화는 왜 보류했나? (★ 시니어급 판단)**

> findings 확정 — **green 검증 자동화 의도적 보류 (트레이드오프):**
> - 현재: green 확인 = 앱 실행 시 **매번 플래그 명령어** (불편하지만 동작)
> - 보류 이유 = 자동화 시 늘어나는 운영 비용:
>   1. green 배포 환경이 **qa/prod별로 각각** 추가
>   2. green 환경의 **환경변수 관리 + API 요청 처리** 필요
>   3. **빌드를 한 번 더** 하게 됨 (데스크톱 앱 특성)
>   4. **데스크톱 blue-green ≠ 웹**: 아티팩트 교체 방식이라 green은 되는데 blue에서 실패 가능
> - → "어렵진 않지만 **운영 피로도 대비 가치를 따져 의도적 보류**" = 단순 구현력이 아닌 **판단력**

---

## 면접 답변 스크립트 (2~3분)

> 인프라 카드 = "당시 못 푼 문제 → 나중에 재설계"의 성장 서사 + 라이브러리 내부 깊이 + 운영 비용 트레이드오프 판단. 빌드 시간은 체감값으로만.

"제가 DentBird의 두 Electron 데스크톱 앱, Batch랑 Linker의 빌드·배포·자동 업데이트 인프라를 맡았습니다.

처음 Batch를 만들 때는 빌드를 제 로컬 PC에서 직접 돌려서 Azure Storage에 올리는 방식이었어요. 그러다 보니 제 PC를 못 쓰면 빌드가 멈추고, 빌드 방법을 아는 사람도 저뿐이라 요청이 다 저한테 몰렸습니다. Windows는 코드사인 USB를 인프라팀이 관리해서 매번 인프라팀을 거쳐야 했고요. 당시엔 이걸 다 풀진 못했는데, 팀이 개편되고 P004를 맡으면서 본격적으로 재설계했습니다.

Mac은 notarize에 필요한 파일을 DevOps 라이브러리에 올려 자동화했고, Windows는 제 PC를 아예 빌드머신으로 세워서 인프라팀 의존을 끊었어요. 그다음 Azure Pipelines가 클라우드 러너라 빌드가 20분 넘게 걸려서, GitHub Actions에 self-hosted 러너를 붙이는 쪽으로 직접 이관했습니다. 지금은 6분 내외로 줄었어요.

자동 업데이트 쪽에서는 electron-builder를 골랐는데, 기본 Publisher가 우리 환경에 안 맞아서 라이브러리의 BitbucketPublisher 클래스를 직접 까서 hostname 버그랑 timeout, Bearer 인증까지 패치해봤습니다. 결국 운영에는 yaml만 커스텀하는 쪽으로 단순화했지만요. 그리고 개발 중에, 코드사인 없이 빌드한 아티팩트를 나중에 따로 서명하면 checksum이 바뀌어서 자동 업데이트가 실패한다는 걸 알게 됐어요. 그래서 코드사인은 빌드 과정에 포함해야 한다는 걸 배웠고, 실제 배포 파이프라인은 그렇게 구성했습니다.

지금은 blue-green으로 배포하는데, S3에 올리는 순간 자동 업데이트가 바로 나가버려서 검증 전 배포 위험이 있어요. green 검증을 자동화할 수도 있지만, 데스크톱 앱은 웹이랑 달라서 환경이 qa/prod별로 두 배가 되고 빌드도 한 번 더 해야 합니다. 그 운영 비용 대비 가치를 따져보고 일단은 수동 플래그 확인으로 두고 의도적으로 보류한 상태예요."

**꼬리질문 대비:**
- 빌드 시간 정확한 수치? → "CI 로그로 정밀 측정은 안 했고, 20분대에서 6분 내외로 체감상 크게 줄었다"
- "1인 개발 아니냐"? → Batch는 **팀 개발, 본인이 최다 기여자**. 인프라/빌드/자동업데이트를 본인이 전담
- Custom Publisher 왜 안 썼나? → "클래스 패치는 유지보수 부담이 커서, 동작은 같되 더 단순한 yaml 커스텀으로 갔다"

---

## 본문 반영 위치

- `career-description.md` § **신설 카드: Electron 데스크톱 빌드·배포·자동 업데이트 인프라**
- `career-description.md` § 프로젝트 4(Linker) / 5(Batch) — 인프라 서술 슬림화 + "동일 인프라 공유" 참조
- `resume.md` § 신설 또는 Batch/Linker 카드 인프라 줄 정렬

---

## ⚠️ 본문 반영 시 함께 정정할 기존 오류 (findings 확정)

| 위치 | 기존 | 정정 |
|------|------|------|
| career 5 역할 | "1인 개발" | **팀 개발, 본인 최다 기여자** |
| career 5 규모 | "13개 버전 (v0.0.1~v1.0.13)" | git 태그상 v0.0.1 근거 없음 → **"v1.0.13까지 릴리즈"** |
| career 4 기간 | "2024.08 ~ 현재 (18개월)" | linker-app 레포 **2024.07~2025.12**, 이후 solutions 통합 |
| career 4-3 외 | "Chrome PNA" | **Chrome LNA** (Local Network Access) — 전부 정정 |
| career 4 성과 | "854MB→78MB (webpack DefinePlugin)" | 실체 = **초기 번들링 미적용 → 적용**. 역공 위험 → **약화/제외** |
| career 4-3 | "4,000줄 문서 작성" | **"AI 활용 20가지 방안 탐색 + 장기 안정성 기준 Custom Protocol 의사결정"** |

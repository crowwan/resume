# 05. DentBird Batch — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (1차 발굴 2026-06-01, 빌드 파이프라인까지. 자동 업데이트는 추후 인터뷰)
> 발굴 대상:
> - `workspace/dentbird-batch-client` (초기 Batch v1, Bitbucket)
> - `devops/dentbird-solutions/apps/batch/` (현재 P004)

---

## 0. ★ Batch 3세대 구조 (사용자 설명 — 발굴 메타)

| 세대 | 위치 | 누가 | 본인 역할 |
|------|------|------|----------|
| **초기 Batch (v1)** | `workspace/dentbird-batch-client` | **현재 팀 되기 전, 본인 팀** | 배포·자동 업데이트·프로젝트 세팅·기능 개발 일부 |
| **Batch P002** | (legacy-batch, 레포에서 삭제됨 — git log 확인 가능) | **다른 팀**이 재개발 | 버그 수정 정도 |
| **Batch P004 (현재)** | `devops/dentbird-solutions/apps/batch/` | 팀 개편 후 002 넘겨받아 개발 | **P002·P004 빌드 파이프라인 구축** |

- P002는 곧 P004로 전환 작업 예정이라 레포에서 삭제됨 `[git log 확인 가능]`
- **★ career 5번 "1인 개발" = 오류.** 팀 개발이며 본인이 **최다 기여자**(초기 batch jwkim 347커밋 / 2위 126) `[git: shortlog]`

---

## 1. 의사결정 흔적

### ★ 빌드 파이프라인 재설계 (핵심 카드 — 성장 서사 정석)

**초기 빌드 방식의 문제 (당시 미해결):**
- 빌드 = **로컬 PC에서 직접 빌드 → Azure Storage 업로드** `[기억]`
- 문제 1: **빌드 담당 PC를 못 쓰는 순간 난감** (속인성)
- 문제 2: **빌드 방법 아는 사람이 담당자뿐**
- 문제 3: 빌드/배포 요청이 담당자에게 몰려 **커뮤니케이션 비용** 발생
- → **당시엔 해결 못 함** (다른 팀이 P002 개발하게 됨)

**팀 개편 후 P004 개발하며 재설계 (← "당시 미해결 → 나중에 재설계" 성장 서사):**
1. **Mac 파이프라인 먼저**: notarize 필요 파일을 **DevOps library에 추가**해 빌드 자동화 `[code: batch-native/azure-pipelines/batch-native-macos.yml]`
2. **Windows 파이프라인 이후**:
   - 원래: 코드사인 **USB를 인프라팀이 관리**, 빌드도 인프라팀이 진행 (다른 팀도 같은 USB 필요)
   - 문제: 매번 불편 + 인프라팀 없으면 빌드 어려움
   - 해결: **본인 윈도우 PC를 빌드머신**으로 파이프라인 구축 `[code: batch-native-windows.yml]`
3. **DevOps(Azure) Mac 파이프라인**: Azure 클라우드 러너 → 느림 (**빌드 20분+**)
4. **GitHub Actions 전환 (본인 주도, 2026-04)**: Azure Pipelines → **GitHub Actions + self-hosted 러너** 이관 `[git: dentbird-solutions #7689 "GHA 빌드 파이프라인 이관 및 macOS self-hosted 러너 적용"]`
   - macOS: `runs-on: [self-hosted, macOS, ARM64]`, 영구 캐시 경로(`imago-builds`)로 BrokerServer 부하 회피, notarize(`xcrun notarytool`) `[code: build-batch-native-macos.yml]`
   - Windows: `runs-on: [self-hosted, Windows, solution-windows-builder]`, `signtool` 서명 검증, **NAS 직접 업로드** `[code: build-batch-native-windows.yml]`
   - 추가 본인 커밋: macOS universal 빌드+자동업데이트 정상화(#7861), NAS 직접 업로드 전환·artifact 제거(#7929), self-hosted runner 안정화(#7884), Win version 전파 회귀 수정(#8195) `[git]`
   - **Phase 0.3 ~ 4 단위로 체계적 진행** `[git: 커밋 메시지]`
   - 현재 **빌드 6분 내외** (Azure 클라우드 러너 20분+ 대비) `[기억/체감]`

## 2. 측정 가능한 변화

- **빌드 시간 20분+ → 6분 내외** (Azure 클라우드 러너 → self-hosted iMac) `[기억/체감]` `[측정필요: CI 실행 로그]`
- 빌드 속인성 제거: 담당 PC·담당자 의존 → 파이프라인 자동화 (정성적) `[기억]`
- 인프라팀 의존 제거: 코드사인 USB·인프라팀 빌드 → 자체 빌드머신 (정성적) `[기억]`

## 3. 후속 영향

- 초기 batch에서 **인식했지만 못 푼 문제**(로컬 빌드 속인성)를 → **P004에서 파이프라인으로 재설계** = 성장 서사
- `docs/plans/2026-03-19-pipeline-unification-plan.md` 존재 → 파이프라인 **통합** 작업 진행 중 `[code]`

## 4. 운영 패턴

- 초기 batch에서 본인이 반복 담당한 영역: **배포 / 자동 업데이트 / 프로젝트 세팅 / 빌드 / 코드사인 / 버전 관리** `[git]`
  - DB-453 windows code sign (PR #184), DB-237 배포 환경 변경 (PR #159), DB-276 실행 중 업데이트, DB-309 version 동적 표시, datadog 빌드 설정, webpack 빌드 스크립트 `[git]`

## 5. 기술 스택 (사용 중)

- 초기 Batch: Electron, React, TypeScript, **CRACO**, electron-builder, **forge**, webpack `[code: workspace/dentbird-batch-client — craco.config.js, forge.config.js, electron-builder.config.js]`
  - (career 기재: Recoil, VTK.js, i18next, Axios — 재확인 예정)
- P004: NX 모노레포(dentbird-solutions) 내, batch-native(Electron) + batch-web + batch-cli + batch-worker-server 구조 `[code]`
- CI/CD: Azure Pipelines (Mac/Windows yml, 과거) → **GitHub Actions self-hosted** (macOS ARM64 / Windows `solution-windows-builder`) `[code: .github/workflows/build-batch-native-*.yml]` — 본인 이관 #7689
  - 관련 workflow: build-batch-native-{macos,windows}.yml, cutover-dev(P002→P004 전환 PoC), build-legacy-batch-desktop-*(P002 baseline), test-batch-perf.yml `[code]`

---

## 6. ★ 자동 업데이트 / 배포 전략 (인터뷰 raw — 두 번째 큰 기둥)

### 초기 batch 자동 업데이트 (Bitbucket 시절) `[Confluence: 본인 작성 4건, 2024 — ✅ 읽음]`
- 방식: 회사 **Azure Storage** 아티팩트 → `download.dentbird.com` + **Front Door** 경유 업데이트 `[기억]`
- **electron-forge vs electron-builder 의사결정**: 커스터마이징 쉽고 자료 많은 **electron-builder 선택** `[405373813]`
- **기본 publish 불가 → Custom Publisher 직접 구현(실험)**: electron-builder의 `BitbucketPublisher` 클래스를 분석해 **hostname 누락 버그 수정·timeout 120s→20분·Bearer 토큰 인증**까지 직접 패치 `[405373813]` — ★ **라이브러리 내부까지 파고든 깊이** (electron-builder issue #6192 기반)
  - 실제 batch 적용은 클래스 대신 **업데이트용 yaml만 커스텀**하는 방향으로 단순화 `[기억]`
- **QA/PROD 분리 배포 전략**: 같은 파일 바라보면 사용자가 QA 버전을 받는 사고 위험 → `publish.channel` 조정으로 **채널별 latest.yml 분리** `[406850081]`
- **checksum mismatch 트러블슈팅**: 빌드 후 **수동 코드사인 → 바이너리 checksum 변경 → latest.yml의 sha512와 불일치**로 업데이트 실패. sha512 재계산해 yml 수정으로 해결 `[565282665]`
  - ★ 이 **"수동 코드사인의 부작용"이 곧 빌드 파이프라인 자동화(§1) 동기와 직결** — 두 기둥이 한 서사로 연결됨
  - mac 업데이트 시 `checkForUpdates` 중복 호출 이슈도 기록 `[890798124]`
- 시도/탈락: Bitbucket 파이프라인 빌드→즉시 자동업데이트 하려 했으나 **코드사인이 인프라팀 소관이라 무산** `[기억]`

### 현재 (P004) 자동 업데이트 + 배포 경로
- 저장소: Azure Storage → **S3** 전환
- 빌드 주체: 인프라팀 → **GitHub Actions** (단 **S3 업로드는 여전히 인프라팀** 소관)
- **QA 빠른 설치 경로 설계**: 빌드 → 빌드머신(self-hosted, NAS 마운트)이 **사내 NAS에 직접 업로드** → 인프라팀이 NAS→S3 → 자동 업데이트
  - = QA팀이 자동 업데이트 안 기다리고 **NAS에서 최신 버전 바로 설치** 가능 `[code: 파이프라인 NAS publish]`

### 진행 중 설계 고민 (성장 서사 — 면접 강점)
- 목표: **S3까지 파이프라인이 직접 업로드**
- 막힌 이유: **S3 업로드 순간 자동 업데이트가 즉시 발동** → 검증 전 배포 위험
- 현재 **blue-green 배포** 구성됨. green 검증 = 앱 실행 시 **특정 플래그 명령어**로 green 환경 확인
- 최종 고민: Electron 앱(**batch, linker**) 모두 blue-green 가능하게 **S3 구축** 검토 중
  - → ⚠️ batch/linker 공통 = **카드 경계 주의** (자동업데이트/배포는 Electron 앱 공통 주제. Linker 카드와 조율 필요)

- **green 검증 자동화 보류 — 트레이드오프 분석 (★ 시니어급 판단, 면접 강점)**:
  - 현재: green 확인 = 앱 실행 시 **매번 플래그 명령어** (불편하지만 동작). 자동화하고 싶으나 보류 중
  - 보류 이유 = 자동화 시 늘어나는 운영 비용:
    1. green 배포 환경이 **qa/prod별로 각각** 추가
    2. green 환경의 **환경변수 관리 + API 요청 처리** 필요 (환경변수발 문제 우려)
    3. **빌드를 한 번 더** 하게 됨 (데스크탑 앱 특성)
    4. **데스크탑 blue-green ≠ 웹**: 아티팩트 교체 방식이라 **green은 되는데 blue에서 실패** 가능
  - → "어렵진 않지만 운영 피로도 대비 가치를 따져 **의도적 보류**" = 단순 구현력이 아닌 **판단력** 어필

### 출처 문서 (Confluence — 본인 작성, ✅ 읽음 2026-06-01)
- `405373813` Electron 빌드/배포/자동업데이트 with bitbucket (electron-builder 선택, Custom Publisher 직접 구현·hostname/timeout/Bearer 패치)
- `406850081` [WIP] Electron 배포 전략 (publish.channel로 QA/PROD 분리)
- `565282665` electron update checksum mismatch (수동 코드사인 → sha512 불일치)
- `890798124` Batch update error (mac checkForUpdates 중복 호출)

---

## 미해결 / 추후

| 항목 | 상태 |
|------|------|
| 빌드 시간 20분/6분 정확값 | `[측정필요]` — CI 실행 로그 (Azure/GitHub Actions 웹) |
| self-hosted 러너 설정 | ✅ 확정: GHA self-hosted (macOS ARM64 / Windows `solution-windows-builder`), 본인 이관 #7689 `[code/git]` |
| **자동 업데이트** 서사 | ✅ 1차 정리(§6). blue-green green 검증·S3 직접업로드 트레이드오프는 인터뷰 보강 |
| 초기 batch 버전수 (career "v0.0.1~v1.0.13, 13개") | git tag 검증 필요 |
| P002 legacy-batch 삭제 시점 | git log 확인 가능 |

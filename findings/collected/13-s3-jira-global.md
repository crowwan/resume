# 13 — S3 아티팩트 + 전역 Jira (정량/범위 증거 보강)

> 수집일: 2026-06-05. 역할: `arn:aws:sts::453632314549:assumed-role/Non-Infra-Team/jwkim@imagoworks.ai` [aws: sts get-caller-identity]
> raw 사실만. 서사 X. 모든 수치는 측정 시점 스냅샷의 **실제 값**이며 추정·반올림 없음.
> ⚠️ S3 객체는 본인 단독 산출물이 아님(빌드/CI/팀 공용 버킷). "본인 기여"가 아니라 **본인이 관여한 영역의 규모·범위 증거**로만 사용. 기여 분리는 기존 findings(04-electron-infra, 02-test-e2e, 05-mfe 등) 참조. 모호한 항목은 [규모증거] 태그.

---

## A. S3 — Electron 인스톨러/배포 아티팩트 (정량)

### A-1. download-prod 버킷 (운영 배포 인스톨러)
출처: [aws: s3 ls download-prod-ap-northeast-2-f7288bce --recursive --summarize]
- 버킷 총량: **Total Objects 28, Total Size 1.1 GiB** (prod 스냅샷)
- **Batch 인스톨러** (`batch/`):
  - Windows: `Dentbird_Batch_Setup_1.1.85.exe` = **97.9 MiB** (+ .blockmap 105.5 KiB)
  - macOS: `Dentbird_Batch_Setup.pkg` = **127.8 MiB**, `_1.1.85.zip` = **123.0 MiB**
  - 버전 라인: prod = **1.1.85**
  - auto-update 매니페스트 존재: `release.yml`, `release-mac.yml` (electron-updater 규약) [규모증거]
- **Batch Experimental 채널** (`batch/experimental/`) — prod와 분리된 별도 릴리스 채널:
  - Win `Dentbird_Batch_Experimental_Setup_2.0.2.exe` = 76.9 MiB, mac `.pkg` = 99.6 MiB, `.zip` = 96.1 MiB
  - 버전 라인 = **2.0.x** (prod 1.1.x와 별개로 운영), 자체 `release.yml`/`release-mac.yml` 보유
  - → **prod / experimental 2개 채널을 각각 Win+mac 멀티플랫폼으로 빌드·서명·배포하는 파이프라인 운영 증거**
- **Linker 인스톨러** (`linker/`):
  - `Dentbird_Linker_Setup_1.0.5.exe` = **78.1 MiB**, prod 버전 = **1.0.5**

### A-2. download 환경별 버킷 (dev/qa/test 분리 운영)
출처: [aws: s3 ls download-{dev,qa,test}-* --recursive --summarize]
- `download-dev` : 28 objects / **1.3 GiB**. Linker dev 채널 `Dentbird_Linker_Dev_Setup_1.0.1-beta.1.exe` = 77.0 MiB, 매니페스트 `dev.yml`
- `download-qa`  : 25 objects / **1.2 GiB**. `Dentbird_Linker_QA_Setup_1.0.5-2.exe` = 77.9 MiB, 매니페스트 `qa.yml`
- `download-test`: 0 objects (비어있음 — 현재 미사용)
- → **dev / qa / prod 채널별 독립 다운로드 버킷 + 채널별 update 매니페스트(dev.yml/qa.yml/release.yml) 분리**. Electron 배포 채널 분기 운영의 직접 증거.

### A-3. 정량 요약 (이력서 인용 가능 값)
- Electron 데스크톱 앱 **2종**(Batch, Linker)을 인스톨러로 빌드·배포
- 플랫폼: **Windows(.exe) + macOS(.pkg/.zip)** 멀티플랫폼
- 채널: **dev / qa / prod + experimental** (앱별 버전 라인 prod 1.1.85·Linker 1.0.5, experimental 2.0.2)
- 인스톨러 크기대: Batch ~98–128 MiB, Linker ~78 MiB
- 환경 버킷 합계 규모: dev 1.3 GiB / qa 1.2 GiB / prod 1.1 GiB

---

## B. S3 — 테스트/품질 자동화 아티팩트 (E2E 범위·규모 증거)

### B-1. automation-test-results 버킷 (E2E 결과 저장소)
출처: [aws: s3 ls automation-test-results-ap-northeast-2 --recursive --summarize]
- 버킷 총량: **Total Objects 56,415 / Total Size 20,500,292,298 bytes (≈ 19.1 GiB)** (스냅샷)
- 구조: `monitoring/` (정기 모니터링 런), `_smoke-test/`, `common/`
- 모니터링 러너 멀티리전 운영: `monitoring/eks-runner-apne2/`, `monitoring/eks-runner-use1/`, `monitoring/dykim-mac.local/`
  - **eks-runner-apne2 런 디렉토리 수 = 10,026개**, **eks-runner-use1 = 6,693개** (스냅샷)
  - ⚠️ apne2 런들의 타임스탬프 범위 = **2026-05-18 ~ 2026-06-05 (약 18일)** → 누적 총량 아님, **버킷 라이프사이클 보존 윈도 내 스냅샷**으로 해석. 18일에 1만 런 ≈ 고빈도 정기 실행 패턴 [규모증거]
  - 런 ID 포맷: `2026-05-19T00-01-33-49it/` 식 (분 단위 간격으로 다수 — 짧은 주기 모니터링 시사) [규모증거]
- → **EKS 기반 러너로 apne2 + use1 멀티리전에서 E2E 모니터링을 고빈도 자동 실행**하는 인프라 규모 증거. 기여 분리는 02-test-e2e.md 참조.

### B-2. dentbird-artifacts 버킷 (E2E fixture + VR 베이스라인)
출처: [aws: s3 ls dentbird-artifacts/* --recursive --summarize]
- `e2e-fixtures/` : 200 objects / **56.8 MiB**. `baseline/tooth-library/...` 하위에 `.drc`(Draco 압축 메시) `.featureinfo` 등 — **3D 치아 라이브러리 fixture를 E2E 베이스라인으로 S3에 보관** (예: `Pontic_47.drc` 192.6 KiB)
- `storybook-vr/` : 526 objects / **26.2 MiB** — Storybook **Visual Regression** 런 산출물
- `storybook-vr-baseline/` : 88 objects / **1.06 MB** — VR **baseline 스냅샷 88장** (디자인 시스템 시각 회귀 기준선) [규모증거]
- `lambda-cloud-storage-proxy/` : 별도 prefix 존재 (서버리스 프록시 아티팩트)
- → **3D 메시 fixture E2E + Storybook 시각 회귀(VR baseline 88장)** 두 축의 품질 자동화가 S3에 물리적으로 존재. 03/07/10 findings 보강.

---

## C. S3 — MFE(마이크로 프론트엔드) 앱 카탈로그 (범위 증거)

### C-1. clients-prod-web-assets (운영 정적 자산 = MFE 앱별 배포 단위)
출처: [aws: s3 ls clients-prod-ap-northeast-2-web-assets-b358d6d0]
- 버킷 총량: **17,756 objects / 12,630,121,675 bytes (≈ 11.8 GiB)** (스냅샷)
- top-level prefix = 앱(또는 remote) 단위, 각 앱마다 `<app>` + `<app>_preview` 페어 운영
- **비-preview 배포 앱 24개** [aws: grep -c 검증]:
  `account, archive, batch, career, cloudbo, clouddesktop, cloudmobile, connect, connect-bo, console, crown, crown-expt, crownbo, explorer, export, hub, landing, milling, modeler, module-federation, offline, payment, setting, studio, viewer`
  - 그중 **`module-federation` prefix가 독립 존재** → Module Federation 호스트/공유 런타임이 별도 배포 단위로 존재하는 직접 증거 (05-mfe.md 보강)
  - 각 앱에 `_preview` 미러 존재 → **앱별 프리뷰(스테이징형) 배포 채널**을 운영 정적 호스팅 레벨에서 분리
- 환경 분리: `clients-{dev,qa,test,prod}-...-web-assets` 4개 버킷 별도 존재
- → **단일 셸 + 24개 MFE 앱 + module-federation 공유 런타임**을 prod 정적 호스팅에 앱별 prefix로 분리 배포. MFE 아키텍처의 규모를 객체 스토리지 레벨에서 입증. (본인 기여 분리는 05-mfe.md / 04-electron-infra.md)

---

## D. 전역 Jira — 본인 담당 이슈 범위·도메인 분포 (운영 패턴 증거)

출처: [jira: searchJiraIssuesUsingJql, jql="assignee = currentUser() ORDER BY updated DESC", maxResults=50, cloudId 29dab47b...]
※ **최근 updated 기준 상위 50건 윈도**(전체 누적 아님). 패턴 해석용. created 범위 = 2023-02-17 ~ 2026-06-04, updated 범위 = 2026-05-12 ~ 2026-06-05.

### D-1. 프로젝트(제품) 분포 — 무엇을 반복 책임졌나
- **DEN (Dentbird Cloud / Bugs): 34건** ← 압도적 다수
- **CRWN (Dentbird Crown): 11건**
- D1 (Dentbird 1): 3 · CP (Change-point Mgmt): 1 · CLOUDINFRA (Cloud Infra): 1
- → 주력 책임 영역 = **Dentbird Cloud + Crown** 두 제품. 인프라(CLOUDINFRA)·체인지포인트(CP)까지 cross-cutting 으로 걸침.

### D-2. 컴포넌트 분포 — 어떤 기능 도메인을 반복 책임졌나
- **Viewer: 12** (최다) · Cloud - My Designs: 7 · **Export / Export to SW 합계: 12**(Export 6 + Export-to-SW 6) · New Case: 5 · Batch: 3 · Header: 2 · Cloud - Dashboard: 2 · Explorer 1 · Cloud - Subscription 1
- → 반복 책임 도메인 = **3D Viewer / Export(외부 SW 연동: Medit·Shining3D 등) / My Designs / New Case**. 즉 **3D 렌더링 + 외부 스캐너/CAD 연동 + 케이스 관리**가 핵심 운영 슬라이스.

### D-3. 라벨 분포
- Cloud: 34 · Crown: 8 · Batch: 3 (제품 라인 = Cloud 중심, Crown·Batch 병행)

### D-4. 이슈 타입·상태 (운영 성격)
- 타입: **버그 45 / 작업 2 / 에픽 1 / 스토리 1 / Change-point 1** → 이 윈도는 **품질·운영(버그) 대응 비중이 압도적**. = "출시 후 운영·안정화"를 지속 담당하는 패턴
- 상태: 종료 21 / 해결됨 11 / 다시 열림 3 / QA 테스트 중 3 / 나머지 진행·신규 → **종료+해결 32/50 = 처리 완료율 높음**, "다시 열림 3"은 회귀 재대응 흔적 [규모증거]

### D-5. 도메인 키워드 (요약 샘플에서 반복 등장)
- 3D/메시: "텍스쳐", "Mesh color", "렌더러", "Mesh 색상이 Crown 솔루션과 다름"
- 외부 연동: "Medit link", "Shining 3D", "Export to SW", "ALPHA AI"
- 멀티플랫폼/디바이스: "Mobile - 브라우저가 데스크톱 사이트", "일부 모바일 기기"
- → Viewer 3D 렌더링 일관성 + 외부 스캐너 로그인/케이스 로딩 + 모바일 호환이 반복 이슈 영역.

---

## E. 보조 — 멀티리전 운영 규모 (참고)
출처: [aws: s3 ls | grep dentbird-files-prod]
- `dentbird-files-prod-*` 버킷이 **9개 리전**: ap-east-1, ap-northeast-1, ap-northeast-2, ap-south-1, ap-southeast-2, ca-central-1, eu-west-3, us-east-1, us-west-2
- → 서비스가 **9개 글로벌 리전**에 파일 스토리지를 둠. (프론트 본인 기여 아님 — 서비스 글로벌 규모 맥락 [규모증거])

---

## F. 신규 vs 기존 findings 대비 (NEW 표시)
- **NEW**: Batch experimental 2.0.x 별도 채널 / 채널별 update 매니페스트(dev.yml·qa.yml·release.yml) / 인스톨러 정확 크기(MiB) / download 환경 버킷 규모 → 04-electron-infra 보강
- **NEW**: automation-test-results 19.1 GiB·런 1.6만+ / EKS 멀티리전(apne2+use1) 러너 / 분 단위 모니터링 간격 → 02-test-e2e 보강
- **NEW**: e2e-fixtures 3D `.drc` 베이스라인 / storybook-vr baseline 88장 → 02/07/10 보강
- **NEW**: clients-prod 비-preview 앱 24개 + `module-federation` 독립 prefix + 앱별 `_preview` 채널 → 05-mfe 보강
- **NEW**: 전역 Jira 컴포넌트 분포(Viewer 12 / Export·SW 12 / My Designs 7) = 반복 책임 도메인 정량화 → 06/11/12 보강
- 기존 findings에 이미 있던 LNA·Page Object·Module Federation 도입 서사 등은 **재적재 안 함**.

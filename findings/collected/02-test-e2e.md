# 테스트 자동화·E2E·격리 재현·AI 변경감지 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 조회일 표시 생략
> 형제 파일: `../09-test-automation.md` (기존 findings). 이 파일은 거기에 **없던/보강된** 사실만.

## 본인 기여 식별 요약

- **본인(jwkim) = git 커밋·PR·Jira 티켓으로 증명되는 실행 작업.** 이 영역 Jira D1-* 티켓 31건 전수 assignee=currentUser(jwkim), 전부 status=완료. 즉 격리 daily·시각회귀·detect·BO E2E 의 **티켓 실행 주체는 본인**. [jira: assignee=currentUser, 31건] `[본인 — 티켓 실행]`
- **팀-hckim = 위키 문서화 주체.** ★ Dentbird1 스페이스의 이 영역 Confluence 문서(E2E 격리환경·Local Dev Stack·Billing E2E·시각회귀 가이드·E2E 호스트 farm)는 **전부 author/최종수정 = Hyeoncheol Kim(hckim)**. 따라서 이 문서들은 "팀 공유 SoT / 서비스 맥락"으로만 인용하고, 본인 기여는 git/jira로 별도 증명한다. **작업 실행(본인) vs 위키 정리(hckim) 분리.** [confluence: 2287206604·2286158153·2355003615·2356150364·2369061053·2399174662 author=hckim] `[팀-hckim 문서]`
- **본인+AI = Claude와 함께 만든 자작 도구·메트릭**(시각회귀 metric helper, TC Verify 스킬, root-cause 수정 PR). 커밋 author는 본인.
- 모호 항목은 `[기여확인]` 태그.

---

## 1. 의사결정 흔적

### E2E 실행 인프라 3단 진화 — Jira로 시점 확정 (본인 주도)
- ★ **K8s 통합 스케줄러 → EC2+SSH → EC2 환경구성** 3단 진화의 Jira 타임라인: D1-868(K8s 통합 스케줄러 Phase 1, **2025-11-21**) → D1-888(Monitoring Docker 이미지 최적화·환경변수 표준화, **2025-11-23**) → D1-899(EC2 환경 구성, **2025-11-25**). 전부 완료. [jira: D1-868/888/899] `[본인]`
- (09에 없음·강화) **K8s→EC2 재전환 명시 근거: EKS ~$150/월 → EC2 ~$30/월(1/5 비용)** + 선택 재실행(`--grep`)·`--headed`/`PWDEBUG=1` 디버깅·git pull 즉시 반영. [git: dentbird-solutions@84dea72] `[본인]`

### 격리 E2E 환경 설계 SoT — "Build Once Deploy Everywhere" (팀 문서 / 본인 티켓 실행)
- ★ **격리 풀스택 핵심 설계**: PR마다 frontend(S3 release 아티팩트)+backend(ECR 이미지)+MongoDB(single-node replica set `--replSet rs0`, transaction 지원)+MinIO(S3호환, Azure Blob 대체)+nginx 를 Docker Compose로 격리 기동. **공유 dev/qa 환경 의존 제거, 빌드 없이 환경 구성**. platform-server healthcheck ~100초. [confluence: 2286158153] `[팀-hckim 문서 / 본인 티켓 실행]`
- ★ **격리환경 핵심 기술이슈 10건 해결 카탈로그(문서화됨)**: Gateway RequestUriHostPredicate(nginx Host 고정), form-encoded body 소실(login nginx 직접 프록시), account-client publicPath/isUnified, OpenApiRsaKeyService(PKCS#1 RSA), Spring Security CSRF Origin, mongo standalone write 거부 → `--replSet rs0`+mongodb-rs-init bootstrap(Phase 7-L), Spring Session JSON 직렬화 sessionId quote, Azure Blob→MinIO, account-client basename(CLOUD-2415). [confluence: 2286158153 §핵심기술이슈] `[기여확인 — 문서는 hckim, 일부 본인 PR]`
- ★ **세션 직접 주입 설계**: `create-local-sessions.mjs`가 서버 API 호출 없이 Redis에 Spring Session 직접 삽입 + Playwright StorageState JSON 생성(UUID→Redis 해시, JWT refresh HS256 고정시크릿). 서버 기동 없이 세션 생성. 9종 세션(PRO/FREE/EMPTY/US_PRO/JP_PRO/BO_ADMIN/BO_DEALER/member-owner/member). [confluence: 2286158153 §세션관리] `[팀-hckim 문서]`
- ★ **빌드서버 원격 E2E 실행(`dhub e2e` CLI)**: build-server(192.168.0.230)에서 격리 스택 자동 기동+Playwright+결과 콜백. run_id별 Docker 프로젝트명/포트/디렉토리 격리로 동시 다중 실행, flock 자원경합 직렬화. 지원 앱 cloud/batch/account/backoffice/crown. [confluence: 2286158153 §dhub] `[기여확인]`
- ★ **docker/e2e → docker/stack 분리 + 격리 개발환경(`--frontend-dev`)**: 격리 E2E 스택 위에 특정 앱만 호스트 dev-server(`nx serve` HMR)로 swap. 옵션 A(dev-proxy 통합)/B(nginx 프록시 오버라이드)/C(로컬 빌드 S3 대체) 중 **B 채택**(nginx 직접 host proxy → dev-proxy 불필요). `--serve` 포트 자동 기동, 8앱 location 매핑 SSOT(`frontend_dev_app_meta`). [confluence: 2287206604] `[팀-hckim 문서]`

### 시각 회귀 — 도입 WHY가 문서로 존재 (★ 강력한 근거)
- ★ **시각회귀 도입의 근원 WHY = "QA 회귀 보드 폭증 근원 분석"(14일간 버그 103건: CRWN 86·DEN 17)**. 분석 결론: 103건 중 약 절반은 functional E2E가 구조적으로 못 잡는 결함(시각·디자인·transient UI). **시각 회귀 어설션 = 0건**이라 mesh 색상 6건(C8)은 통째로 사각지대. → P0 처방 1번 "시각 회귀 1셋 도입". [confluence: 2356150364] `[팀-hckim 문서 — WHY 근거로 인용]`
- ★ **시각회귀 도입 5선택 기술 의사결정**: D1 = **Storybook+Chromatic 아니라 격리 E2E 위 Playwright `toHaveScreenshot`**(도입비용 ~0, 3D/VTK 실제 렌더 캡처 가능, 격리 fixture 결정성). D2 baseline git 보관(LFS 미도입, 100MB/5000컷 임계 전). D3 **baseline은 격리(Linux Docker)에서만 생성**(`ignoreSnapshots: STAGE!==local`). D4 단일 chromium-linux baseline. D5 **시간은 마스킹 아닌 고정**(Mongo seed 고정 epoch `2026-01-01T00:00:00Z` + `page.clock.install`, 서버 clock은 명시적 비고정 — token TTL/cron 부작용 회피). [confluence: 2355003615] `[팀-hckim 문서]`
- (09에 기본형만 있음·보강) ★ **시각회귀 metric 자체구현(Playwright 기본 한계 우회)**: `--use-gl=swiftshader --disable-gpu-vsync`(CI Linux↔local macOS GPU차 흡수), viewport 1600×1000(variance=0 실증), pngjs 기반 mean‖Δ‖ helper(`compareMeshRender`) 자작. crown-cloud "CAD/3D 렌더 검증 금지" 정책의 명시적 예외. [git: dentbird-solutions@fc3ed17, PR #9517 DEN-4539] `[본인+AI]`

### 격리 daily 아키텍처 ADR — 병렬 폐기 (본인 결정)
- (09에 없음) **app 매트릭스 병렬 폐기 → 토폴로지별 backend 1개 공유+app 순차+전환 시 backend restart(B-pragmatic 채택)**. B-strict(live 재시드)는 cache 위험으로 기각. 근거: "테스트 RUN 시간이 부팅을 압도(cloud 19분·login-matrix 50분 vs 부팅 수십 초)". R0 확정: 2그룹(SR 6앱 / MR 2앱) 2-way 동시 → wall-clock ≈ 2h, nightly 수용. [vault: decisions/2026-06-04-e2e-serial-shared-backend-reuse.md] `[본인 결정]`

### Billing E2E 매트릭스 (★ 새 영역 — 결제 lifecycle 회귀 자동화)
- ★ **결제·구독 lifecycle 매트릭스 E2E 설계 = scenarios.ts 단일 진실원천 + readiness 가드**: 13 시나리오 그룹(checkout/payment-failure/lifecycle/seats/webhook/one-time-charge/plan-change 등), entry에 id/group/priority/lane/status/appAssertions 명시. `matrix-readiness.test.ts`가 entry↔시드↔spec 정합을 매번 강제(한쪽만 추가하면 fail). **Billtap = 검증 대상 아닌 인프라**(Stripe 호출/webhook 로컬 흉내 헬퍼), 가드 대상은 platform-server 비즈니스 로직 + webhook→cache evict 통합 회귀. [confluence: 2369061053] `[팀-hckim 문서 / 본인 billtap run-path fix는 본인]`

---

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

- ★ **Billing 매트릭스 자동화율 — active 시나리오 45건 중 42건 자동화 = 93.3%**(2026-05-12 기준). lane 분포: pr-smoke 21 / daily-isolated 15 / nightly-lifecycle 15. 잔여 3건만 미자동(needs-app-flow 1=정책 미정, needs-billtap-scenario 2). [confluence: 2369061053 §11] `[팀-hckim 문서 — 수치 그대로 인용]`
- ★ **시각회귀 기대 차단율 — QA 분석 86건 중 23건(≈27%)이 시각회귀 1셋으로 차단 가능**(C8 mesh 6/6 + C12 빈상태 4/4 + C1 5/9 + C4 5/9 + C3 3/7). [confluence: 2356150364 §7, 2355003615 §7] `[팀-hckim 문서 — 수치 인용]`
- ★ **QA E2E 어설션 표면 정량(시각회귀 도입 전 baseline)**: `apps/e2e/batch/tests/isolated/` 20파일 중 `toMatchSnapshot/toHaveScreenshot` = **0**, snackbar 어설션 = 2파일, isolated/queue = 1파일, `tests/sds/` = 71파일. → "커버리지는 늘었으나 어설션 표면은 가시성/존재 위주로 좁다"의 정량 근거. [confluence: 2356150364 §1.3] `[팀-hckim 문서]`
- ★ **시각회귀 RED/GREEN 실증**: cascade 회귀 빌드 baseline md5 cc4f10d2/101,698B vs RED 7554d944/63,671B, **diff 23,496/726,960 = 3.232%**. Playwright `maxDiffPixelRatio:0.1`(10%)로는 통과(catch 실패) → 자작 `compareMeshRender(meanThreshold=2.0)`: GREEN mean=0.0000, RED mean=2.2865 FAIL. nginx ro bind mount 재배포 ~1분. [git: fc3ed17, vault: work-summary §3.6] `[본인+AI]`
- ★ **이벤트 기반 wait로 spec 가속**: case-thumbnail-visual.test.ts hard-coded `waitForTimeout(60s+30s)=90s` polling → `page.waitForFunction(getThumbnailDiagnostics().scanCount>=2 && resultantCount>=15)` event-driven. test2 단독 ~120s→19.7s, **전체 spec 120s→37.7s(-68%)**. [vault: learnings/2026-05-28-spec-event-driven-wait-mesh-load-polling.md, commit 1466bbc1363] `[본인]`
- ★ **레거시 TC "격리 편입" 마이그레이션 누계 ~141 TC**(per-PR 명시값 합산, G-4 시리즈 21커밋·격리편입 라벨 17커밋). 실제 총량은 더 큼(일부 PR TC 수 미표기 → [측정필요]). [git: 다수 PR] `[본인]`

---

## 3. 후속 영향

### 격리 회귀 가드 동반 prod 버그 수정 (본인 PR) ↔ QA 보드 폭증 청크 교차검증
- (09에 부분) DEN-4194(#10428) 썸네일 오로라 패턴 fix+회귀가드(prod code ~13줄), DEN-4539 IOS 스캔 메쉬 검정(#9404)+3앱 cross-app 정합 가드(#9655), CRWN-3294(#9036) Batch Review over-exposed 톤, CRWN-3387/3488 Medit/Shining3D import 무한 spinner(libarchive worker URL), DEN-4583(#9670) Export to SW binary leak. [git: 다수] `[본인]`
- ★ **위 회귀들이 문서의 "QA 보드 폭증 86건" 청크에 직접 매핑**: CRWN-3294/3275/3254/3251 = C8 mesh 색상(6건), DEN-4549/4541/4540/4539 = C8 Viewer 색상/텍스처. → **본인이 fix+가드한 회귀들이 팀 근원분석 문서에 그대로 박혀 있음**(본인 작업과 팀 분석 교차 검증). [confluence: 2356150364 §C8, 부록] `[본인 fix + 팀 문서 분류]`

### 격리환경이 "못 잡는" 한계 (성장 서사 — 정직)
- (09에 없음·강화) ★ **격리 자연치유 함정**: 격리 nginx가 `/libarchive.js/` 절대경로를 cloud-desktop `/cloud/` base로 alias → prod의 404 비대칭 시뮬 못 함(CRWN-3488 Shining3D 무한로딩). stub 응답 형태 단순화(`.ply`만 노출 → `Archive.open` unzip flow 우회). **"격리 E2E 통과해도 prod에서 깨지는" 함정** → 진짜 검증은 prod build minify dist grep + manual QA. [vault: learnings/2026-05-21-isolated-stack-structure-and-prod-asymmetry-natural-healing.md] `[본인 발견]`
- ★ **격리환경 제한사항이 문서로 명시됨(설계 한계 인지)**: Javet arm64 미지원(OpenAPI JS 실행 불가), 외부 서비스(Stripe/SendGrid) 더미, Modeler/Milling Server 미포함, 실제 CAD 바이너리=placeholder stub(3D Viewer 정밀검증은 별도 fixture 필요), AI Queue 미포함(Designing/Paused 시뮬 불가→BLOCKED), Electron 미포함. [confluence: 2286158153 §제한사항] `[팀-hckim 문서]`

### AEAD 격리 self-seed (정밀 재현 역량 — 09에서 흡수했으나 상세 보강)
- (09에 흡수됨·상세) ★ **dev S3 바이너리가 AES-256-GCM ciphertext라 fixture를 KMS envelope 구조로 직접 decrypt 재현**: payloadEncoding 매트릭스(AEAD_ENVELOPE_V1=AES-256-GCM / APPLICATION_ENCRYPTED=XOR 0xff / PLAINTEXT). 9-step self-seed 파이프라인, gzip magic 정합 **6/6**. server CASE_OUTPUT_SUB_DOMAIN path-based 분류 우회(case-output→case-input patch). [vault: learnings/2026-05-27-aead-envelope-isolated-self-seed.md] `[본인]`

### EC2 Claude AI 변경감지("e2e detect") — Jira로 시점·동작 확정
- ★ **구축 시점 Jira로 확정**: D1-987(10분 크론잡 계획, 2025-12-01) → D1-992(스킬 문서, 12-01) → D1-993(테스트+EC2 배포, 크론 `*/10`, 로그 로테이션, 12-01) → D1-997(프롬프트 개선, 12-01) → D1-1000(kevin 브랜치 비교 개선, 12-02) → **D1-1058(Playwright HTML 리포트 생성+Teams 알림 개선, 2025-12-04)**. 즉 **2025-12-01~04 집중 구축**(09의 12-01~02보다 D1-1058로 12-04까지 확장). [jira: D1-987~1058, assignee=currentUser] `[본인]`
- ★ **동작 상세(Jira 본문)**: ① 스크립트가 kevin(메인 dev 브랜치)로 전환+pull → ② **State file 기반 커밋 비교(이전 kevin commit vs 현재 kevin)** — EC2가 feature 브랜치 체크아웃 시 엉뚱한 결과 나오던 버그를 D1-1000에서 수정 → ③ Claude 코드 분석으로 관련 TC 추론 → ④ `playwright test --grep` 실행 → ⑤ **Playwright HTML 리포트(`--reporter=list,html`) 생성 + Teams 알림에 리포트 URL 포함**(`https://ai-krc.dev-dentbird.com/tc/playwright-report/`). [jira: D1-1000/1058/993 본문] `[본인]`
- (09에 부분·강화) ★ **detect 운영 한계(성장 서사)**: ① EC2 보안정책으로 TC Manager API 접근 불가 → Teams 웹훅 회귀, ② **2026-05-28부터 daily schedule 실패 누적 → 현재 미사용**, `e2e-local-daily.yml` 재활성화 프로젝트(2026-W23)로 전환. [vault: 2026-W23-e2e-daily-revival.md] `[본인]`

---

## 4. 운영 패턴

### 격리 daily 만성 red 4-root 진단 (인프라 디버깅 역량 — ★ 새 영역)
- ★ **만성 red 4-root 진단**: 최소 2026-05-20부터 schedule이 한 번도 green 아님. 4개 독립 root 분리 — **A 동시성**(8 app 매트릭스가 max-parallel 없이 self-hosted 단일 호스트 16-runner에서 같은 SHA frontend tarball 동시 S3 다운로드 → DNS 마비 → Bring up 무증상 exit 1) / **B redis generic alias 충돌**(#10762, hckim) / **C billtap run-path**(#10783 본인) / **D 개별 spec**. **단일 app dispatch 재현으로 동시성=유일 변수 확정**. [vault: 2026-W23-e2e-daily-revival.md, decisions/2026-06-04-*] `[본인 진단]`
- ★ **root A 근본수정(PR #10759, `dffd8e5`)**: stateful prepare 잡이 `--prefetch-frontends`로 SHARED_CACHE 1회 warm → 후속 app cache hit. fetch-depth 1→50+reference alternates로 prefetch↔app SHA 정합(dispatch 26923897425 prefetch=db79c78 vs app=3c1a3ea 불일치 실측 후 수정). 검증 3회 → single-region 6 app Bring up 통과. [git: dffd8e5] `[본인+AI]`
- ★ **root C 해소(PR #10783, `c4a11a7`)**: billtap ADR-0004 run-scoped(`/runs/<id>`)인데 테스트 헬퍼가 bare `/v1/` 호출 → resolveBilltapRunPath()+4 호출부 prefix+유닛 3케이스. billing run 실측: **billtap 404/missing 0건(前 ~28건), passed 5→14**. [git: c4a11a7] `[본인]`

### E2E 실행 인프라 호스트 구성 (self-hosted 다중 호스트)
- ★ **호스트 farm 구성**: build-server(192.168.0.230, HDD `build-machine` 16 slot + NVMe 12 slot) / **E2E Runner 데스크톱(192.168.10.192, i7-11700/32GB, 매일 새벽 ec2devtest cron 06:00 KST가 리버스 터널로 git pull+Web E2E+Electron E2E, SDVR freshness 3일내 자동 충족)** / sihyung PC(192.168.10.24, WSL2 4-slot `build-sihyung`, HDD→SSD 부하분산). **e2e-*는 docker stack+dev-server 8~12GiB라 sihyung 부적합 → build-server 유지**. [confluence: 2399174662] `[팀-hckim 문서 / 본인 e2e-farm 운영]`
- (09에 있음) **결과 보고 파이프라인**: JUnit XML(GitHub Checks)+HTML 리포트+TC Manager 커스텀 리포터(`libs/e2e/tc-manager-reporter`)+Qase 동기화(Crown)+Teams(Power Automate). [code: .github/workflows] `[본인]`

### TC 작성 자동화 스킬 (★ 09에 없음)
- ★ **TC Verify 스킬 자작(E2E 작성 자동화)**: TC Auto-Verify v2(#5815)→v3 Planner+Verifier·Page Object YAML·tc-api.mjs(#5931)→hint/worker(#5940), `/suite-check` 감사 스킬(#6201). [git: 380e6c4, 37efe41, 6a74896] `[본인]`
- ★ **BO E2E 대량 작성(2025-11 집중, Qase 매핑)**: D1-775(Login BO-425)부터 D1-803/815/823/828/845~865/902 등 Backoffice Suite TC 다수(Create new user/Change password/Workspace invitation/Subscription history export/Session multiple-login BO-615~616/Plan downgrade 6 cases). + D1-945/946/956 BO Page Object 리팩토링·Shared Utils 마이그레이션. [jira: D1-7xx~9xx, assignee=currentUser] `[본인]`
- ★ **2026-04 E2E 품질 작업**: D1-4499(Cloud PO Locator 품질 개선), D1-4465(Setting Suite TC 전수 점검+정리). [jira: D1-4499/4465] `[본인]`

---

## ★ 기존 findings/이력서에 없던 새 증거 (요약)

1. **시각회귀 도입 WHY가 데이터로 존재**: QA 보드 14일 103건 근원분석 → "어설션=0 사각지대" → P0 처방. 본인 fix 회귀들이 이 분석 청크(C8 등)에 직접 매핑. [confluence: 2356150364] (← 단순 "도입했다"가 아니라 "왜·얼마나 차단 가능한지 데이터로 정당화")
2. **Billing lifecycle 매트릭스 E2E 자동화 93.3%(42/45)** — scenarios.ts SSOT + readiness 가드. [confluence: 2369061053]
3. **격리 daily 만성 red 4-root 진단 + 본인 2개 PR 근본수정**(#10759 동시성, #10783 billtap, billtap 404 ~28건→0). [vault, git]
4. **자작 시각회귀 metric**: Playwright `maxDiffPixelRatio 10%`가 못 잡는 mesh 회귀를 pngjs mean‖Δ‖로 RED/GREEN 실증(mean 0.0→2.2865). [git: fc3ed17]
5. **이벤트 기반 wait 전환으로 spec -68%(120s→37.7s)**. [vault, commit 1466bbc1363]
6. **AI detect 시점·동작 Jira 확정**(12-01~04), HTML 리포트 URL까지. [jira: D1-987~1058]
7. **E2E 인프라 K8s→EC2 비용 1/5(~$150→~$30/월)** 명시 근거. [git: 84dea72]
8. **TC Verify v2/v3 스킬 자작**(E2E 작성 자동화 도구). [git]

---

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- AI 변경감지가 **실제로 잡은 회귀 구체 사례**(2025-12 운영 중) — git/Teams/Jira 모두 미발견. [기억]/[측정필요]
- 격리 docker setup.sh `--up` cold-start **정확 소요 분/초** — 문서엔 "platform-server ~100초"만 정량. 전체 cold-start [측정필요]
- "격리 편입 누계 ~141 TC"는 본인 커밋 subject 명시값 합산 → 실제 총량 더 큼. 정확 집계 [측정필요]
- 시각회귀 baseline 가이드(2355003615)는 **계획/설계 문서** — 5단계 PR 중 본인이 실제 머지한 범위(PR 1~6)는 fc3ed17(metric 자작분)만 확인됨. 나머지 git 확인 필요 [측정필요]
- 격리환경 핵심기술이슈 10건 중 **본인 PR로 해소한 항목 vs hckim/타인 해소 항목 분리** — 문서는 hckim 명의라 본인 슬라이스 정확 매핑 [기여확인]
- Billing 매트릭스 자동화율 93.3% 수치는 **hckim 문서값** — 본인이 자동화한 시나리오 수 vs 팀 합산 분리 [기여확인]
- 호스트 farm 구성·dhub e2e CLI는 hckim 문서 SoT — 본인의 e2e-farm 운영/구축 슬라이스가 어디까지인지 [기여확인]

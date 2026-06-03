# _ 서비스/플랫폼 개요 — "우리 서비스를 설명할 수 있게" (면접 대비)

> **1차 목적 = 팀 작업 전반 이해** (면접 *"회사 서비스/기술을 설명해보세요"* 대비). 본인이 했든 안 했든, **우리 팀이 한 작업을 내가 알고 설명할 수 있게** 하는 게 핵심. 사용자 방침(2026-06-03): "내가 했고 안했고가 중요하다기보단 우리 팀에서 진행한 작업들을 내가 알고 있는 게 더 중요."
>
> **기여 태그는 보조** — 나중에 *이력서 bullet을 고를 때만* 참고: `[본인]`(개인 성과로 써도 됨) / `[팀]`·`[팀-김현철]`(설명은 OK, 개인 성과로 쓰면 발명) / `[기여확인]`. 면접에서 팀 작업을 말할 땐 "팀에서 이렇게 했고 나는 이렇게 이해한다"가 정직하고 충분히 강함.

---

## 1. 서비스 개요
- **DentBird** — 이마고웍스의 AI 기반 치과 CAD/CAM SaaS, 글로벌 B2B. 제품: cloud(cloud-desktop), crown, modeler, milling, batch.
- 멀티테넌트(highdental/ci_medical/idd 등) + 멀티리전(KR, US_EAST 등 10여 리전).

## 2. 플랫폼 아키텍처
- **dentbird-solutions 모노레포(NX)**: 흩어진 앱·서버 통합.
- **MFE 공통 모듈**: iframe→빌드타임→iframe(same-origin `/cloud/module.html`). `[본인]`(초기 주도)+`[팀]`(최종)
- **런타임 Config(Build Once Deploy Everywhere)**: 빌드 1회→config.js만 교체. `[본인]`(핵심설계 참여)+`[팀]`
- **격리환경 커밋 재현**: setup.sh --sha. `[본인]` 다수

## 3. 빌드/배포 최적화 ★ (정량 강력 — 대체로 [팀])
- **CI 빌드 시간 456s → 40~60s (약 -89~91%)**, 6주 트렌드(2026-03~04). [Confluence 2284322880] `[팀]` + `[본인 일부]`(어느 기법인지 확인 후 01 bullet)
  - shared node_modules hardlink snapshot: **pnpm install 193s→2s(-99%)**
  - Webpack→Vite(빌드 60s→5s), GitHub Actions 전환 + **NX affected**(변경 앱만 빌드), tsc incremental(19s→1s)
- **Build Once Deploy Everywhere**: qa/prod 배포 **120s→11s(-91%)**(아티팩트 promote + config.js 교체). [Confluence 2284322927] `[본인 참여]+[팀]`
- **cold start 최적화**: nx daemon/crown-client, Windows Defender exclusion(56.5s→~40s). [Confluence 2358378605] `[팀]`
- **prefetch viewer 속도**: `libs/embed-modules/.../ViewerDialog/prefetch/` — `prefetchViewerMesh()` + LRU 캐시(5). [code] `[팀/기여확인]`

## 4. 관측성 (Datadog) — [팀], 문서 작성자 hckim
- **Datadog 본격화**: 로그인/인증→구독/결제→케이스→viewer→export 8개 핵심 흐름 계측. RUM `usr.id` 표준화, gateway/platform APM trace, 구조화 로그(`obs.domain/event`, `error.code`). [Confluence 2356150511] `[팀-김현철]`
- **EB→Datadog 표준 정렬**: `logAsyncError` 단일 진입점으로 5개 앱 수렴. [Confluence 2364440747] `[팀]`
- 호주 업/다운로드 RUM 30일 분석 등 RUM 기반 가설검증. `[팀]`

## 5. 백엔드 아키텍처/통합 — [팀]
- **Milling Worker 얇음화**(Service Bus→Prepared Worker, platform-server 진실원천). [Confluence 2394423468] `[팀]`
- **Crown 생성 오프로드→milling-worker 통합**(taskType 라우팅, 자식 spawn, 세마포어). [Confluence 2425946261] `[팀]`
- **WIP Lock Poll API 단일화**(`/status/poll` 5초 hub + 5 subscriber, Redis shadow+Mongo fallback). [Confluence 2300411905] `[팀]`
- **Preference/Setting/Preset 도메인 통합**(CRWN-3355, UserConfig/Preset 신규 컬렉션, 13 PR). [Confluence 2394882059] `[팀]`
- **인증 JWT→Redis 세션** → findings/10. `[팀]` 핵심, `[기여확인]` 본인 FE측
- **파일 스토리지 S3 + AEAD 암호화** → findings/10. `[팀]` 핵심

## 6. 인증·보안 → findings/10-auth-security
- JWT→Redis 세션, 파일 AEAD 암호화(KMS envelope). 대체로 `[팀]`, 본인 범위 `[기여확인]`.

## 7. 결제(Stripe)/구독 — [본인](Account FE)+[팀]
- **Account 구독제 전환 FE 전담**(플랜 업그레이드·시트 구매·쿠폰·결제수단·히스토리). → findings/04. `[본인]`
- Stripe 미국→싱가폴 법인 이전, Connected Account Direct Charges(P010, 딜러 Stripe `Stripe-Account` 헤더). [Confluence 2426143016, 2265415798] `[팀]`

## 8. 측정/Analytics (GA4) — [본인?](Account/Landing 연관)+[팀]
- **GA4 이벤트 택소노미 v2 — 281개 이벤트**("코드에서 읽는" 네이밍 `GA4.{app}.{feature}.{action}`). [Confluence 2250408398] `[팀]`
- **Landing→Account→Subscription 전환 측정 갭**: UTM/click id 미보존으로 attribution 단절. 택소노미·구조는 팀. **본인은 Account 일부 이벤트 연동**(2026-06-03 확정). [Confluence 2383052830] `[본인 일부 이벤트]+[팀]` → 04에 작은 bullet
- Desktop Analytics(Metabase + dhub CLI). `[팀]`

## 9. 멀티테넌트/도메인 — [팀] 설계, [본인] Account FE 연동
- 테넌트 배정(IP 독점국가 → 서브도메인 → 기본), 한번 배정 후 고정. `?tid=` 파라미터(화면 브랜딩만). [Confluence 2193588330] `[팀]`
- 서브도메인 통합 리서치(region path-prefix vs `?tid=`). [Confluence 2391769104] `[팀]`

## 10. 테스트·자동화 → findings/09-test-automation
- Playwright E2E(642스펙·13제품, Page Object, visual regression). EC2 Claude `e2e detect`. `[본인]` 주도

## 11. ★ AI 도구·엔지니어링 문화 — ⚠️ 대부분 [팀-김현철], 본인 아님!
> 사용자가 "Claude Code 핸즈온 등"을 언급했으나, **Confluence 작성자 전부 김현철(hckim, ba204367)**. 본인 성과로 오인 금지. 본인 참여/영향은 [기여확인].
- Claude Code 핸즈온 프레젠테이션 대본(발표자=hckim). [Confluence 1947041920] `[팀-김현철]`
- dentbird-bot(Teams AI 봇) 아키텍처·운영(D1팀·김현철). [Confluence 2389016603] `[팀-김현철]`
- CLAUDE.md 구조 개선 의사결정(6,446 커밋 분석). [Confluence 2218426594] `[팀-김현철]`
- Batch CLI AI Agent 연동 기획. [Confluence 2339733648] `[팀-김현철]`
- 타입스크립트 챌린지 스터디 운영(주최=hckim). [Confluence 1757544577] `[팀-김현철]` / 본인 참여 여부 `[기여확인]`
- PR Complexity Leverage 분석 도구. [Confluence 2285895870] `[팀-김현철]`
- 팀 봇 계정 운영(작성=Jungwon Lee). [Confluence 2275508256] `[팀]`

## 12. 제품 도메인 — DentBird가 무엇을 하는 서비스인가 ★ (면접 핵심)
- **DentBird Cloud (cloud-desktop)**: 치과 디지털 **케이스 관리·디자인·공유·내보내기** 플랫폼. Dashboard/Case List/My Designs, Case Info·Share·3D Viewer, 계정/구독(My Info·Subscription·Payment·Member/Workspace), Export(티어별 다운로드 할당). [Confluence 2412052482]
- **DentBird Batch**: **AI 기반 다건 자동 설계** 처리. 케이스를 Queue에 적재 → Batch Setup → 백그라운드 Designing → Confirm/Reject. Max Concurrent(Performance 모드), Preset(파라미터 템플릿), Local Queue Actor(상태머신 race 방지). [Confluence 2409824295]
- **도메인 용어**: Case(치과 케이스), Workspace(조직), Sheet(좌석=구독 제한), WIP Lock(동시편집 방지 점유), Margin(보철물 가장자리)·Abutment(받침)·Crown(금관)·Milling(CNC 가공)·Modeler. `[팀]`
- **SDS/UX 인벤토리 작업**: 품질 등급 A 목표 — QA 막판 발견되는 기준 불일치·회귀를 줄이려 **SPEC 860개 원자화**(Cloud 348+Batch 106+Modules 7) + Storybook/E2E/시각회귀 증거 연결 + PR 게이트(specs-lint) + Daily 리포트. [Confluence 2408841232, 2398191635] `[팀]`

## 13. QA 체계 — [팀]
- **release 주기 사이클**: QA 회고 → 회귀 원인 분석 → 테스트 전략 개선 → 버그 수정 가드레일. release/2026-02는 단일앱 E2E 481개, 버그 160건(P1 37) 분석. [Confluence 2190409791]
- **회귀 보드 폭증 근원 분석**(14일 103건): 12청크 분류 → 5대 근원(SDS 미갱신, E2E 어설션 좁음/시각회귀 0, 공유토큰 부재, mutation oracle 비표준 `invalidateQueries` 34 vs `useMutation` 10, 기획-개발-QA 계약 동기 실패). [Confluence 2356150364]
- **QA→E2E transition**: Jira 296건 레거시 분류(manual/complex-repro/not-reproducible) + dhub로 660건 transition. release/2026-04에 세션·크로스리전·결제 엣지 E2E 80개 추가 계획. [Confluence 2297495553, 2372075613, 2190704703]
- **FE/BE 버그 수정 가드레일**: 원인 분석 → 회귀 방지(타입>유닛>E2E) → 느슨한 방어 회피. 세션 버그는 KR+US_EAST 2리전 검증 필수. [Confluence 2282946692] → 09 E2E와 연결. 본인 E2E 기여와 맞물림 `[본인?]`

## 14. 마케팅/CRM 메시징 (Customer.io) — [팀], FE 연동 주목
- **자체 ReleaseNote(What's New 모달+API) → Customer.io 인앱 메시징 단일화**(2026-05 단일 배포 사이클). announcement/education/reinforcement 사용자 유도. [Confluence 2356150563]
- **FE 연동**(`libs/customer-io`): `<CustomerIOPageTracker/>`(단일 page() 호출), `<CustomerIOInAppGate/>`(Datadog Feature Flag 게이트), `useCustomerIOTracking`. 무한 노출 사고 방지(MutationObserver→WeakSet 격리, kill-switch, PoC 5종). Crown/Modeler/Batch/Account 통합. [Confluence 2406350891] `[팀]` (FE 연동이라 본인 기여 가능 `[기여확인]`)

## 15. 배포/릴리스 운영 — ★ [본인?] 가능성 높음
- **배포 신체계(2026-04~)**: Azure Pipelines → **GitHub Actions + 앱별 독립 배포 + Build Once Deploy Everywhere**. dev는 `release/**` push→NX affected→변경 앱만 빌드, qa/prod는 **Git 태그(`{env}/{app}/{YYYYMMDD-HHmm}`)로 dev 아티팩트 retag**(재빌드 없음). [Confluence 2315944218]
- 클라(8앱) S3+CloudFront promote(~7~15초), 서버(3앱) ECR retag + ArgoCD Blue-Green. NX-skipped 앱은 직전 아티팩트/이미지 manifest를 새 SHA로 copy(모든 release SHA promote 가능 invariant — 2026-04-30 incident 회고). [code: .github/workflows/deploy-clients.yml, deploy-servers.yml]
- **배포 호환성 경계 재정의**: PR 단위 → **앱 간 배포 태그 단위**. 같은 앱 내 PR 완결이면 Parallel Change 불필요, 앱 경계(서버↔클라)·DB/공유인프라는 필수. [Confluence 2299199490, code: .claude/rules/deployment-compat.md]
- **ECR 태그 컨벤션 통일** + 배포 이력 태그 414건 backfill. [Confluence 2274394290, 2301198337]
- **★ 기여**: **본인 일부 기여 확정**(2026-06-03). 독립 카드 대신 **03 MFE "통합 배포 전략" / 01 인프라에 bullet로 흡수**. 구체 범위(클라/서버 파이프라인·호환성 경계 조사 중 무엇을 본인이) 는 카드 작성 시 확정. [git: 본인 일부]

## 16. Open API(파트너 통합) + Dealer 비즈니스 — [팀]
- **Open API**: 외부 파트너(hass/luxcreo/eighteeth 등)가 DentBird Crown을 자기 플랫폼에 임베딩. **구독 위임형 시나리오 B**(파트너가 구독 관리), API Key+OAuth, webhook(case_created/designed/exported), transfer session. [Confluence 2387673224, 2177040407]
- **Dealer 모델**: 일반 Dealer(medit/axsys, subdomain 라우팅) vs **국가별 독점 Dealer**(highdental KR/ci_medical JP, IP 자동배정). Dealer는 카드 미등록(BO 수동 관리, OUT_OF_BAND 결제). [Confluence 2387673224]
- **Pricing 4단계**: Free$0(3건)/Starter$90(30건)/Lite$150(100건)/Pro$400(무제한). Extra Export 후불→**즉시결제** 전환. [Confluence 2177433602]
- **v10 Billing**: `TenantBillingConfig`(SOT) + `BillingRailEvaluator`(CARD vs OUT_OF_BAND, grandfather 룰). → 04 Account 구독 FE와 연결. `[팀]` (Account FE는 `[본인]`)

---

## 발굴 대기 (남은 것)
- prefetch viewer cache 정책 효과, 빌드 최적화 본인 기여분, GA4 attribution 본인 구현 여부, **배포 파이프라인 본인 기여 범위** — 인터뷰로(→ _questions.md).
- **코드 확인 완료**(2026-06-03): 레포 구조 맵 → [_codebase-map.md](./_codebase-map.md) (apps 30+·libs 60+, 본인 연관 lib 표시)

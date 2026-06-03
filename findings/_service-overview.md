# _ 서비스/플랫폼 개요 — "우리 서비스를 설명할 수 있게" (면접 대비)

> 면접 *"회사 서비스/기술 스택을 설명해보세요"* 대비 카탈로그. **본인이 직접 안 했어도** 알아야 할 팀/플랫폼 기술을 모음. (개인 이력서 bullet용 아님)
>
> **기여 태그**: `[본인]` / `[팀]` / `[팀-김현철]`(hckim, authorId ba204367 — 본인 아님!) / `[기여확인]`. 본문엔 `[본인]`만.
> **발명 금지**: `[팀]` 항목을 본인 성과로 말하지 않는다. "팀에서 했고 나는 이렇게 이해한다."

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
- **CI 빌드 시간 456s → 40~60s (약 -89~91%)**, 6주 트렌드(2026-03~04). [Confluence 2284322880] `[팀]`
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
- **Landing→Account→Subscription 전환 측정 갭**: UTM/click id 미보존으로 attribution 단절, 서버 purchase/refund 이벤트 세션 단절. 권장 구조 제안. [Confluence 2383052830] `[본인?]` ← 06 Landing·04 Account 담당이라 연관, 확인 필요
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

---

## 발굴 대기 (남은 것)
- prefetch viewer cache 정책 효과, 빌드 최적화 본인 기여분, GA4 attribution 본인 구현 여부 — 인터뷰로(→ _questions.md).

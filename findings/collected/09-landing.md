# 기업 랜딩 페이지 — Next.js 풀스택 (v3 리뉴얼·Backoffice·서버 API) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 기존 `findings/06-landing.md`에 없던 새/보강 사실만

## 본인 기여 식별 요약

- **본인(jwkim)**: 타입세이프 i18n 시스템 단독 설계(2024-06, 커밋 체인 확정), v3 리뉴얼 윈도우 압도적 단독 주도(2023-09~12 author 383커밋), server LANDING-134 풀스택 슬라이스(Fastify route + Mongoose updateMany), 사내 `@imago-cloud/action-log` SDK 랜딩 진입 이벤트 연동 + SSR navigator 동적 import 우회, i18n 적용에 따른 레이아웃 엔지니어링(underline 재계산·버튼 폭·언어별 스타일훅), LANDING-146 GA ID 교체.
- **타인/팀**: GTM·Datadog RUM·fpixel은 레포 first Init부터 존재(본인 도입 아님, 06-landing.md 확정과 일치). 2024-09 Lokalise 11개 언어 확장 세팅(Kim Uhjin). GA4 택소노미/전환 attribution 설계 Confluence 2건(Hyeoncheol Kim, 2026, 현재 모노레포 — 본인·시점·레포 모두 무관).
- **[기여확인]**: react-query v3+v4 공존 / AutoScrolling 구현 방식이 본인 결정인지 — 이번 단계로도 미해결.

## 1. 의사결정 흔적

- ★ **타입세이프 i18n 시스템 = jwkim 단독 설계, 시점 2024-06-18~19** (커밋 체인 `e2099fbe1`→`8896e8784`→`8075dce05`→`e9f58ac4a`→`de091a574`). `i18next.d.ts`(9줄) + `resources.d.ts`(534줄 자동생성) 신규, **34개 파일**에 적용. (※ 06-landing.md는 "도입" 사실만 있고 시점·기여·규모 미확정 → 본 항목이 NEW) `[git: landing-page-client]`
- ★ **컴파일타임 키 차단 메커니즘 (코드 증거)**: `declare module 'i18next'`로 `CustomTypeOptions` 확장(`resources: Resources` + `defaultNS:'lang'` + `fallbackNS:'lokalise'`). `useTranslate` 훅 prop 타입을 `id: string` → `id: TI18nKey`(`= TFuncKey<'lang' | Namespace<'lokalise'>>`)로 교체 → 존재하지 않는 키는 TS 컴파일 에러. `Translate`/`TranslateInt`/`TString` 3개 API 전부 적용 `[code: src/types/i18next.d.ts] [git: @8075dce05]`
- ★ **자동 생성 파이프라인**: package.json `"interface": "i18next-resources-for-ts interface -i ./src/i18n/locales/en -o ./src/types/resources.d.ts"` — 영어 로케일 단일 소스로 534줄 타입 재생성, `check-types`(`tsc --noEmit`)로 검증 `[git: @de091a574 package.json]`
- ★ **SSR navigator 문제 → dynamic import 결정**: `useImagoEvent`에서 `@imago-cloud/action-log` 정적 import 시 Next SSR 컴파일 단계 `navigator` 미존재로 실패 → `import('@imago-cloud/action-log').then(...)` 동적 import로 우회. 커밋 메시지에 근거 명시 `[git: @901889531 LANDING-107, 2023-10-24]`

## 2. 측정 가능한 변화   (출처에 적힌 수치만)

- ★ **v3 리뉴얼 윈도우 본인 점유율 재확정 (06-landing.md "361 vs 8"보다 강함)**: 2023-09-19~2023-12-31 author 분포 — **jwkim 383, 2위 Kim Uhjin 24, 3위 Daeil Jung 9, 4위 David Ryu 5**. 전체 약 90%, 2위의 약 16배 → "v3 단독 주도" git 확정 `[git: shortlog --all --since=2023-09-19 --until=2023-12-31]`
- ★ **client 레포 전체 author 순위 (참고/주의용)**: jwkim 510 = 전체 6위(상위는 타 서비스 코드 작성자 Yunji Ka 2225 등). 단 v3 윈도우로 좁히면 압도적 1위 → 본문에 "전체 510"은 단독으로 쓰지 말 것(맥락 없이 6위로 보일 수 있음) `[git: shortlog -sne --all]`
- ★ **LCP preload 실제 구현 = Next `<Image priority={true}>`** (Crown Hero / Career Hero 2파일). (06-landing.md는 "LCP 이미지 preload" 기법만 있고 구현 방식 미상 → 방식 NEW) `[git: @361a2d165 LANDING-120, 2023-10-20]`
- ★ **SEO PR #108(LANDING-142) 실측 스코프**: crown/studio 제품 SEO + LANDING-145 main·crown lokalise i18n 동반. **5파일(+11/−9), Approved-by Yunji Ka** `[git: @0f196b3b8]`
- **Lighthouse 점수는 06-landing.md 결론 유지** — 환경차(dev vs localhost)로 본문에 점수 박지 않고 기법 중심.

## 3. 후속 영향

- ★ **타입세이프 i18n 구조의 지속성 (코드 박힘으로 확정)**: 2024-06 `i18next.d.ts` + `resources.d.ts` 자동생성 구조가 이후 Lokalise 기반 다국어 확장의 타입 기반으로 유지. `fallbackNS:'lokalise'`가 코드에 박혀 본인 설계가 후속 Lokalise 워크플로우(타인, 2024-09)와 맞물려 동작. (06-landing.md의 "확장 가능한 구조 설계" 후보를 코드 증거로 보강) `[code: src/types/i18next.d.ts] [git]`
- ★ **v3 릴리스 라인 장기 운영**: `v3.0.0`(2023-10-29 첫 태그) → `v3.0.34`(2026-01-22), **v3.0.x 47개 태그**. 본인이 2023-10 시작한 v3가 2년 이상 단일 메이저 라인으로 운영 중 `[git: tag, for-each-ref]`
- ⚠️ 후속 GA4 측정 고도화·전환 attribution 설계(Confluence 2건)는 **타인(Hyeoncheol Kim, 2026)** 작업 — 본인 i18n/이벤트 구조의 후속이 아님. **두 흐름을 연결하지 말 것.**

## 4. 운영 패턴

- ★ **server LANDING-134 풀스택 슬라이스 코드 확정 (06-landing.md "5커밋" → 코드 디테일 NEW)**: Fastify 라우트에 `PATCH /patch-tag` 1개 신규 + 컨트롤러 `Patch_tag` 핸들러. Mongoose `Join.updateMany(filter, {$set:{department_tag, affiliation}})` 벌크 업데이트 + request.body validation(필수값 누락·조합 검증). 2파일(controller +28/route). 후속 validation 2커밋(`eff3e64`/`54b80f4`) + `isGreetingLinkExist` 제거(`239ecca`) → PR #3 머지(`41844dd`) `[git: landing-page-server @9223d46 LANDING-134, 2023-12-07~12]`
  - **Jira 교차검증**: LANDING-134 "Recruit Tag 및 affiliation 수정 api 추가" = **유일한 Backend Task** (나머지 46건 전부 Frontend Task) → 풀스택 슬라이스의 BE측 단일 이슈로 확정 `[jira: LANDING-134, Backend Task, 2023-12-07]`
  - BO 1주 53커밋(LANDING-129, 06-landing.md §4)과 **같은 주 동시 진행** = "1주 FE+BE 동시 풀스택" 서사에 server 코드 슬라이스(Fastify route + Mongoose updateMany)가 NEW 디테일로 보강
- ★ **이벤트 로그 = 사내 `@imago-cloud/action-log` SDK 연동 (GA4 아님)**: jwkim이 `_app.tsx`에 `ImagoEvent.inflow.enterLanding({referrer: document.referrer})` 추가(`0cd5c02f4` LANDING-120, 2023-10-23) → SSR navigator 문제로 `useImagoEvent` 동적 import 리팩토링(`901889531` LANDING-107, 2023-10-24). GTM/Datadog RUM/fpixel은 레포 first Init(`ba7f7efd1`)부터 존재 = 본인 도입 아님(06-landing.md 확정과 일치) `[git/code]`
  - **Jira 교차검증**: LANDING-146 "GA 신규 ID 추가"(2024-04-22) jwkim assignee — 단 이는 **기존 GA/GTM ID 교체 작업**이지 설계가 아님. description 없음(제목만). → "GA4 택소노미/이벤트 설계"로 표현 금지 `[jira: LANDING-146]`
- ★ **i18n = 단순 번역이 아닌 레이아웃 엔지니어링 동반**: 언어 변경 시 header underline 재계산(`c1a5f57e8` LANDING-114), 번역문 버튼 폭 초과 문제·translateX 계산값화(`b2524d077`/`93959c675` LANDING-111), 언어별 스타일 커스텀훅 `useI18nStyle`(LANDING-122). (06-landing.md는 `useI18nStyle` 존재만 언급 → "왜/무엇을" 디테일 NEW) `[git]`
  - **Jira 교차검증**: LANDING-114 "헤더 UI 버그 수정", LANDING-122 "배포 후 UI 문제 해결", LANDING-144 "일본어 변경 후 home/Product-crown 진입 시 일본어 적용 풀리는 이슈 해결"(2024-04-08) — i18n 적용에 따른 버그 대응이 다수 이슈로 존재 `[jira: LANDING-114/122/144]`
- **Jira = jwkim assignee 랜딩 이슈 47건 (LANDING-99~153 + 후속 버그 CRWN-2040/DEN-4024/DEN-4503), 전부 `종료`**. 단 이슈 본문(description)은 비어 있고 제목만 존재 → 작업 범위 확정엔 충분, 결정 근거·임팩트는 커밋이 1차 출처 `[jira: LANDING-99~153]`

## ★ 기존 findings/이력서에 없던 새 증거

1. **타입세이프 i18n 단독 설계의 코드 메커니즘·시점·규모** — `declare module 'i18next'` + `CustomTypeOptions` 확장으로 컴파일타임 키 차단, 영어 로케일 단일 소스 → 534줄 타입 자동생성(`i18next-resources-for-ts`), 34개 파일 적용, **2024-06** 단독. (06-landing.md엔 "도입했다" 한 줄만) `[git/code @8075dce05~de091a574]`
2. **v3 리뉴얼 윈도우 author 383 vs 2위 24 (16배)** — 06-landing.md "361 vs 8"보다 강한 단독 주도 증거 `[git shortlog 2023-09-19~12-31]`
3. **v3.0.x 47개 태그, v3.0.0(2023-10)→v3.0.34(2026-01)** — 본인 시작 메이저 라인이 2년+ 운영 중이라는 지속성 증거(완전 NEW) `[git tag]`
4. **server 풀스택 슬라이스 코드 디테일** — Fastify `PATCH /patch-tag` + Mongoose `updateMany` 벌크 + body validation. "5커밋"만 있던 것을 코드 수준으로 (NEW) `[git landing-page-server @9223d46]`
5. **이벤트 = 사내 action-log SDK + SSR navigator 동적 import 우회** — "분석 도구는 기존" 결론은 유지하되, 본인이 실제로 한 것(랜딩 진입 이벤트 연동 + SSR 호환 엔지니어링)이 코드로 확정. GA4 설계가 아님을 명확히 (NEW + 리스크 차단) `[git @0cd5c02f4, @901889531]`
6. **Jira 47건 1:1 교차검증** — LANDING-134가 유일 Backend Task(풀스택 단일 BE 이슈 확정), SEO/Lighthouse/ i18n 버그 이슈가 커밋과 정확히 매칭 (NEW) `[jira]`

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- **[기여확인]** react-query **v3 + @tanstack v4 공존**이 본인의 마이그레이션 결정인지, 단순 레거시 혼재인지 — 06-landing.md부터 미해결, Confluence·Jira·vault에도 단서 없음.
- **[기억]** AutoScrolling(무한 횡스크롤) 구현 방식 — CSS 애니메이션 vs JS, 라이브러리 vs 직접 — 결정 근거 미확인. (LANDING-104/111/120 커밋 존재는 확인, 방식은 미상)
- **[측정필요]** Lighthouse 전/후 점수는 환경차(dev 배포 vs localhost)로 직접 비교 불가 → 본문 점수 표기 금지, 기법 중심 유지(06-landing.md 결론 유지).
- ⚠️ **GA4 이벤트 택소노미/전환 attribution 설계는 본인 기여 아님** — Confluence 2건(2250408398, 2383052830)은 전부 Hyeoncheol Kim 작성·2026·현재 모노레포 대상. 시점·작성자·레포 모두 jwkim 랜딩과 무관. **GA4/택소노미/전환설계 서사로 쓰면 면접 리스크.** `[confluence: 2250408398, 2383052830]`
- ⚠️ **vault(dentbird-solutions)에 랜딩 사실 0건** — 랜딩 프로젝트가 vault 운영 시점(2026 CAM/crown 모노레포) 이전이라 흔적 없음. landing 관련 grep 매칭은 전부 현재 `module-explorer.dentbird.com` OAuth 콜백 등 무관 맥락. `[vault: dentbird-solutions/ grep 0건]`

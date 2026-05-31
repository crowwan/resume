# 06. 기업 랜딩 페이지 — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: done (발굴 2026-05-31 / 남은 확인: client "주도" 표현 1건만 사용자 대기)
> 발굴 대상:
> - `devops/landing-page-client` (히스토리 보존 이전 = 최신·완전)
> - `devops/landing-page-server`
> - `workspace/landing-page-bo-client` (DevOps 미이전 → Bitbucket 레포가 유일본)

---

## 0. 레포·기간 지도 (발굴 메타)

- **마이그레이션 = 히스토리 보존**: client/server 모두 `workspace`(Bitbucket)와 `devops`(Azure)의 **첫 커밋 해시가 동일** (client `ee0e5bd` 2022-03-14 / server `4c30936` 2022-03-08). → devops 레포가 전체 히스토리 보유, workspace는 중복. **앞으로 랜딩 발굴은 devops 한쪽만.** `[git: 첫커밋 해시 일치]`
- **BO는 DevOps 미이전** → `workspace/landing-page-bo-client`가 유일본 `[git]`
- **입사=인턴 첫 작업이 랜딩**: jwkim client 첫 커밋 **2023-09-19** `[git]`
- **작업 분포**: client 2023-09 ~ 2025-10. 집중기 = 2023-09(92)·2023-10(243)·2023-11(26)·2023-12(27) = **입사 직후 ~3개월에 v3 리뉴얼**. 이후 2024~25는 산발적 유지보수(배너 추가/제거, i18n 수정, cache time). `[git: 월별 분포]`
- (누적 커밋 수는 이력서 미사용 — 페이스 서사만 채택)

---

## 1. 의사결정 흔적

- `[기억/인터뷰 보강 필요]` 무한 횡스크롤(AutoScrolling) 구현 방식 선택 근거 — CSS 애니메이션 vs JS? 라이브러리 vs 직접?
- react-query **v3** + @tanstack/react-query **v4** 공존 `[code: package.json]` = 마이그레이션 중 or 레거시 혼재 → 내 결정인지 확인 필요
- i18n 타입 세이프 시스템(`i18next-resources-for-ts` 자동 타입생성) 도입 — career 6-2 주장. 내 결정인지 `[기억]`

## 2. 측정 가능한 변화 → **기법 중심으로 서술 (점수 단정 금지)**

### 적용한 성능/SEO 최적화 기법 (전부 git 증거)
- **LCP 이미지 preload** (LANDING-120, 2023-10-20) `[git]`
- **폰트 preload**: Inter, Noto Sans (LANDING-111, 2023-10-17) `[git]`
- **이미지 최적화**: product hero **4x 고해상도** 대응(2024-09-27), quality 조절(LANDING-124/122), 미사용 이미지 제거(다수) `[git]`
- **정적 자원 캐시**: webm/woff 캐시 설정(2025-05-29), compress 설정 테스트(2025-05-26) `[git]`
- **SEO**: 신규 SEO 적용 PR #108(LANDING-142) + HeaderSEO(LANDING-103) + Open Graph image(2024-10-11) `[git]`
- **Lighthouse 개선 PR #105**(LANDING-143, 2024-04-22): 11개 컴포넌트 수정(+36/−14) — Header/Hero/Products 등 `[git: merge bf1fc69]`

### Lighthouse 점수 데이터 (사용자 보관 스크린샷) — ⚠️ 직접 비교 불가
> 개선 전 = `dev.dentbird.com`(배포), 개선 후 = `localhost:3000`(로컬). **환경이 달라 점수 직접 비교는 면접 리스크.** 본문엔 점수 박지 않고 *기법 중심* 서술. `[측정: 사용자 보관 Lighthouse 캡처]`

| 페이지 | 전(dev) 성능 | 후(localhost) 성능 | SEO |
|--------|----|----|----|
| 메인 `/` | 82 | 93 | 100 |
| `company/` | 87 | 97 | 100 |
| `products/crown` | 90 | 88~89 | 100 |
| `products/studio` | 93 | 89 | 100 |
| `company/about` | 97 | 95 | 100 |

- ✅ 일관된 사실: **SEO 거의 전 페이지 100점**
- ⚠️ 성능: 메인·company만 상승, crown/studio/about은 하락 → "성능 일괄 개선" 주장 불가
- 결정: **본문은 "Lighthouse로 측정하며 LCP/폰트 preload·이미지 최적화·캐시·SEO 적용"** 기법 서술. 점수는 면접 구두 보강용으로만(메인 82→93, 단 환경차 인지).

## 3. 후속 영향

- 약함. 랜딩 → 이후 카드로 이어지는 재설계 발판은 아직 미발굴.

## 4. 운영 패턴 / 페이스 서사

- ★ **BO 3계층 조직관리(Groups→Teams→Members) CRUD 0→1을 1주에 53커밋** (2023-12-07 ~ 2023-12-13, 전부 LANDING-129) `[git: bo-client]` → career 6-7 "1주 53커밋" **사실 확정** ✅
- ★ **동시 풀스택**: 같은 1주에 server PR #3 (LANDING-134 recruit tag/affiliation patch API, jwkim 5커밋, 2023-12-07~12) 동시 진행 `[git: server]` → career 6-8(빼기 후보였음)을 BO와 **묶어 "1주에 FE+BE 동시 풀스택으로 조직관리 기능 완성"** 으로 강화
- **무한 횡스크롤 카드 = AutoScrolling 컴포넌트** (LANDING-104/111/120, 2023-10) `[git]` — 사용자 기억과 일치. career의 "Carousel/인터랙티브 컴포넌트"가 이것
- **i18n 대량 작업** (LANDING-100/102/114/121/122/124) + 언어별 스타일 커스텀훅 `useI18nStyle`(LANDING-122) `[git]`

## 5. 기술 스택 (사용 중)

> 태그: `[내 결정]` 직접 도입 / `[기존]` 물려받음·도입주체 불명

- **Next.js 13** (작업 당시 **13.0.5** → 현재 13.5.9) — **Pages Router** (`src/pages/_document.tsx`, `_app.tsx`) `[code/git]`
  - ✅ 확정: 입사 시점(2023-10)부터 Next 13. 사용자 기억 "12"는 부정확
- React 18, TypeScript 4.9 `[code]`
- 상태: @tanstack/react-query v4 **+** react-query v3 공존 `[code]`
- i18n: i18next / react-i18next + **Lokalise**(`downloadLokaliseI18n.ts`) `[code]`
- 애니메이션: **AOS** (Animate On Scroll) `[code]`
- **분석 스택 3종**: **GTM**(`googletagmanager`, `_document.tsx`, `NEXT_PUBLIC_GOOGLE_TAG_MANAGER_ID`) + **Customer.io CDP**(`src/lib/customerio/analytics.ts`, `_app.tsx`) + **Datadog RUM**(`src/lib/dataDog/`) `[code]` `[기존]`
  - ✅ **확정(사용자 확인): 셋 다 입사 전부터 있던 도구. 본인이 도입한 게 아님.** → "분석 시스템 구축" 류 표현 금지. "있던 도구를 운영/연동" 수준만 가능. (사용자 기억 "GA"는 부정확 — 실제 GTM+CDP+RUM)
- 사내 라이브러리: `@imago-cloud/design-system` v2, `storage-client`, `action-log` `[code]` (→ 프로젝트 7 디자인시스템과 연결)
- 폰트: Inter, Noto Sans (JP weight 600) `[code/git]`
- **i18n 언어**: 작업 당시 **ko/en/ja 3개** (career "3개 언어" 정확). 현재는 **11개**(de/es/fr/it/pt/ru/zh_CN/zh_TW 추가) `[git/code]`
  - 확장은 **2024-09 Lokalise 세팅(Kim Uhjin)** — 본인 작업 아님
  - → ③ 후속 영향 후보: "본인이 구축한 타입세이프 i18n 구조가 이후 11개 언어로 확장" (단 Lokalise 도입자=타인이므로 "확장 가능한 구조 설계"로 표현)

### Server 기술 (jwkim 5커밋 = 부분 참여)
- Fastify 3, Mongoose 6, **SendGrid**(메일), node-cron/node-schedule, migrate-mongo `[code: server/package.json]`

### BO 기술
- React 17, MUI 5, **CRA**(react-scripts), react-query v3, **react-quill**(에디터), react-router 6 `[code: bo/package.json]`

---

## 미해결 / 확정 결과

| 항목 | 결과 |
|------|------|
| Lighthouse 점수 | ✅ 해결: 환경차(dev vs localhost)로 점수 단정 X → **기법 중심 서술** (§2) |
| 분석 도구 도입 주체 | ✅ 해결: `[기존]` — 본인 도입 아님. "구축" 표현 금지 |
| Next.js 작업 당시 버전 | ✅ 확정: 2023-10부터 **13.0.5** (기억 "12" 부정확) `[git]` |
| 다국어 확장 | ✅ 확정: 작업 당시 3개(한/영/일). 11개 확장은 2024-09 타인(Lokalise) `[git]` |
| client **"주도" 표현** | ✅ **"주도" 정당 (git 확정)**: 유승현(601)은 입사 전 2023 상반기(2~5월) 구버전 작업자. **v3 리뉴얼(2023-09~11)은 jwkim 361커밋 압도적 1위(2위 8커밋)**. 총량 510 vs 601은 시대 차이 `[git]` |

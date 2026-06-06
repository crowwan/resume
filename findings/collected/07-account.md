# Dentbird Account — B2B SaaS 구독/결제 FE (크레딧→구독, Stripe, FSD) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 4시그널(커밋+PR+Confluence+Jira+vault) 통합본
> 기존 `findings/04-account.md`에 **이미 있는 사실은 제외**하고, NEW/보강분만 적재.
> 본인 = git author `jwkim@imagoworks.ai`. 레포: `dentbird-account-client`(구) · `imago-payment-client`(구, ★NEW) · `dentbird-solutions/apps/{account-client,payment-client,e2e/backoffice}`(모노레포).

---

## 본인 기여 식별 요약

- **jwkim 직접 기여(NEW)**: ① 별도 결제 팝업 앱 `imago-payment-client` 구독 도메인 연동(business-error 처리·add-seats API·postMessage origin 격리) ② Backoffice(운영자) 구독 E2E 스위트 대량 작성 ③ BO 구독 E2E 실행시간 단축(-39%/-80%, D1-860) ④ 결제 Unknown error P1 핫픽스(postMessage origin 빈문자열 → payment-client env 로딩 근본원인) ⑤ 딜러십 분기 운영 버그 다수 전담 ⑥ 폴링 한도 명시화(SUB-407) ⑦ `DealerChecker` 렌더프롭 가드 FE 구현.
- **팀/타인(본인 무관 또는 서비스 맥락용)**: Stripe Connect Direct Charges 서버 설계·JP Direct Debit·US→SG 법인 이전 = [팀-Adam]. Pricing 4단계·Dealer Membership 정책 기획 = [팀-Hyeoncheol Kim]. Confluence 5건은 **전원 팀원 작성** → 본인 성과 아님, 서비스 맥락 설명용으로만.
- 모호: 제품 측 정량 지표(커버리지%·결제 버그 감소율)는 출처 없음 [측정필요].

---

## 1. 의사결정 흔적

### ★ 결제 팝업 = 별도 앱 `imago-payment-client` 본인 구현 (PopupCommunicator의 반대편)
- 결제 플로우는 **부모(account-client) ↔ 팝업(payment-client) 2-앱 postMessage 구조**. 부모 측 `PopupCommunicator`(postMessage 추상화)의 **상대편 팝업 앱이 별도 레포 `imago-payment-client`**, 본인이 구독 도메인 기능을 직접 연동. SUB 브랜치: `SUB-331 add-seats-api 연동`, `SUB-387 payment-client에 business-error 처리`. [git: imago-payment-client@7de159e PR#76] [git: imago-payment-client@b67f912 PR#79] ★
- 팝업→부모 통신: 응답을 `window.opener.postMessage({success, data}, new URL(document.referrer).origin)` 로 **referrer origin에만 전송(origin 격리)**. 성공 페이지는 `IMAGOWORKS_HOST_DOMAIN` origin으로 송신. URL 파라미터 `planId`/`count`/`referrer`로 결제 컨텍스트 전달. [code: imago-payment-client/src/AxiosInstance.tsx] [code: imago-payment-client/src/CheckoutSuccessPage.tsx] [code: imago-payment-client/src/CheckoutLoadingPage.tsx] ★
- (기존 04-account.md는 PopupCommunicator·payment-client 앱 존재 자체를 미기록 → 전부 NEW)

### ★ 멀티테넌트 접근제어 `DealerChecker` — 기획·BE 정책의 FE 구현 측
- `DealerChecker`/`ACCESS_CONTROL` 렌더프롭 가드: 딜러 유저면 결제 액션 대신 link-out 다이얼로그. **팀의 Dealer Membership 정책**("Dealer 고객: 구독은 Dealer가 BO에서 수동 등록, 사용자 Stripe 카드 없음, Extra export 미제공")의 **FE 화면 분기 구현**. [git: dentbird-account-client@7b20eed7 PR#572] [code: features/dealership/components/DealerChecker.tsx] + [confluence: 2177433602, 작성=Hyeoncheol Kim] ★
- 딜러 결제 인프라(Stripe Connect Direct Charges, `Stripe-Account: acct_xxx` 헤더 스코핑, StripeAccountResolver, 7개 Repository RequestOptions) = **전부 서버/플랫폼 설계 [팀-Adam]**. 본인 FE는 정책의 화면 분기만. [confluence: 2265415798 "P010 Stripe Connect Direct Charges", 작성=Adam] `[팀-Adam]`

### Stripe 연동 방향 = Customer Portal 리다이렉트 (자체 결제폼 X) — Confluence 교차확인 (보강)
- (결제수단 관리=customer-portal redirect 자체는 기존 04 §4에 기록됨. **NEW는 문서 교차확인분만**) 팀 기획 문서가 동일 방향 확인: "Billing Portal 유지", "Stripe가 이미 제공"(Billing Portal·Proration·Invoice Preview·Webhook) → 본인 FE는 Stripe 기본기능 위에 redirect/preview 연동. [confluence: 2177433602 "Pricing 정책 변경 + Dealer Membership", 작성=Hyeoncheol Kim] ★(문서 교차)
- 일할(proration) 월→일 전환(`pricePerDay*seatCount*remainingDays`)은 기존 04 미기록 가능 — Stripe Proration 견적 응답을 **본인이 일 단위 모델로 받아 표시**(인터페이스·Adapter·UI). [git: dentbird-account-client@50b94330 PR#536] [code: shared/apis/Adapter.ts] [confluence: 2177433602]

---

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

- ★ **Backoffice 구독 E2E 실행시간 -39%**: D1-860 PR에서 `subscription-history.test.ts`를 `describe.serial`로 묶고 **Export(다운로드)를 1회만 실행 → 4개 테스트에서 Download 객체 재사용**. 실행 1.8분→1.1분(-39%), 다운로드 5회→1회(-80%). [git: dentbird-solutions@18725aaedcc PR#26389] [jira: D1-860] ★
  - ⚠️ 주의: 이 수치는 **테스트 실행시간 / 다운로드 횟수**이지 **제품 성능이 아님**. 이력서에 쓸 때 "E2E 스위트 실행시간"으로 정확히 한정해야 함.
- ★ **폴링 한도 수치(SUB-407)**: 무한 폴링 버그 → base **3000ms**·최대 **20회**·초과 시 `resolve(false)`, `setInterval`→재귀 `setTimeout` 전환, 언마운트 cleanup. [git: dentbird-account-client@31fce352 PR#537] [code: shared/hooks/useCheckoutStatus.ts] ★
- 구독/시트/빌링 도메인 테스트 파일 **33개**(현 src 기준, dealership 도메인 3개 신규 포함). [code: dentbird-account-client/src] — (기존 04는 구 레포 기준 **32개**로 기록 → +1 보강, dealership 3건 추가)
- 제품 측 정량(커버리지%·결제 버그 감소율)은 여전히 출처 없음. [측정필요]

---

## 3. 후속 영향

### ★ Backoffice(딜러/운영자) 구독 E2E 스위트 = 본인 대량 작성 (2025-11)
- dentbird-solutions `apps/e2e/backoffice`에 **Subscription 도메인 E2E 다건 작성**: Subscription History Common(D1-812, 4건), Event date 컬럼(D1-809, 3건), Edit subscription 드롭다운(D1-858, 4건), 탭 구성(D1-828, 1건), Plan Downgrade 모달(D1-902, 6건), History Export(D1-860, 6건), 페이지네이션(5건) 등. Qase TC 다수(BO-2979~3708). [git: dentbird-solutions PR#26389/#26365/#27229 등 다수] [jira: D1-809/812/828/858/860/902] ★
- 의미: **구독 기능을 만든 사람이 운영자(BO) 측 회귀 가드까지 작성** → 구현~품질 종단 책임. (기존 04는 BO E2E 미기록) ★

### ★ 결제 Unknown error 핫픽스 = payment-client 환경변수 근본원인 추적 (P1)
- DEN-4330(우선순위 Highest): **Stripe 결제 완료 후 Unknown error 화면**. 본인 분석 — `postMessage` target origin이 빈 문자열(`Invalid target origin ''`) → `IMAGOWORKS_HOST_DOMAIN` 환경변수 빈 문자열 → **근본원인 = `apps/payment-client/webpack.config.js`가 루트 `.env`를 안 읽음**. 해결: envFiles에 루트 .env 경로 추가(이후 NX 기본 동작으로 변경). [jira: D1-2302/DEN-4330] [git: dentbird-solutions@9f8111e2935 PR#30095] [git: dentbird-solutions@0379d1baee2] ★
- PopupCommunicator의 origin 격리 설계와 직결되는 운영 버그 — postMessage origin이 살아야 부모-팝업 통신 성립. ★

### 결제 성공 후 상태 동기화 폴링 (eventual consistency)
- 업그레이드/재개/취소 모두 `checkSubscriptionStatusInterval` 폴링 → `invalidateQueries(['my-subscription-plan'])`. [code: UpgradePlan/components/PlanDetail.tsx] [code: shared/hooks/useCheckoutStatus.ts] (§2 SUB-407 폴링 한도와 연결)

### 멀티테넌트 API 정합(workspace-id 제거)
- member API `/me/` 경로 정리(SUB-521), 구독 API 전체 표면(`upgrade-estimate`/`seats/buy`/`workspace-plan-subscriptions`/`cancel`/`stop-pending-cancellation`/`coupons`/`payments/customer-portal`/`payments/history`/`my-tenant`). [code: shared/constants/apiPath.ts]

---

## 4. 운영 패턴

- **SUB 프로젝트(구독제 전환) 사실상 단독 FE 드라이브**(2024-10~2025-03, SUB-8~SUB-654 연속). approver = Adam·Seunghyun Yu·Sangin Lee. [git: --author 집계] ★(SUB 프로젝트 키 범위·승인자 NEW)
- **장기 운영 버그 전담**: 2025-02~03 DEN-38xx, 2025-07 DEN-40xx. 시트추가 팝업 닫힘 시 스피너 유지(DEN-4076), 취소 후 redirect 누락(DEN-4072), 해지사유 200자 제한(DEN-4067), Plan upgrade 모달 잉여 문구(DEN-4514), Free 플랜 취소버튼 미노출(DEN-3890). **딜러십 유저 분기 버그 다수**(DEN-3886/3887/3888/3913/3930, DBO-1087 = billing cycle 공란). [jira: 본인 assignee 36건] ★(Jira로 운영범위 정량화)
- **구독 도메인 = 신규 구현(account-client/payment-client) → 운영 버그픽스 → 운영자 측 E2E 가드(BO)까지 한 사람이 종단 담당**. [git+jira 종합] ★

---

## ★ 기존 findings/이력서에 없던 새 증거

1. **별도 결제앱 `imago-payment-client` 본인 구현** + 부모-팝업 2-앱 postMessage origin 격리 구조(SUB-331/387). [git: imago-payment-client@7de159e PR#76, @b67f912 PR#79]
2. **BO(운영자) 구독 E2E 스위트 본인 대량 작성** — 구현자가 운영 회귀 가드까지(D1-8xx, Qase BO-29xx~37xx). [jira: D1-809/812/828/858/860/902]
3. **BO 구독 E2E 실행시간 -39% / 다운로드 -80%** 정량(Download 객체 재사용 + describe.serial) — 이 카드 영역에서 **유일한 출처 확보 정량**. 단 테스트 실행시간임(제품 성능 아님). [git: dentbird-solutions@18725aaedcc PR#26389] [jira: D1-860]
4. **결제 Unknown error P1 핫픽스** — postMessage origin 빈문자열 → payment-client env 로딩 근본원인 추적(DEN-4330). [jira: D1-2302/DEN-4330] [git: dentbird-solutions PR#30095]
5. **폴링 한도 명시화 수치**(base 3000ms·최대 20회) — SUB-407. [git: dentbird-account-client@31fce352 PR#537]
6. **딜러십 운영 버그 다수 전담** + `DealerChecker` FE 가드 — Jira로 운영 범위 정량화(DEN-3886~3930, DBO-1087). [jira: 본인 assignee 36건] [git: @7b20eed7 PR#572]
7. (서비스 맥락만) Stripe Connect/JP Direct Debit/SG 법인 이전은 **전부 [팀-Adam] 서버·플랫폼 문서**, 본인 직접 기여 증거 없음 → 면접에서 "팀이 했고 나는 FE 측에서 이렇게 대응"으로만. [confluence: 2265415798/2276163909/2426143016]

---

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

1. **제품 측 정량 부재**: 결제 버그 감소율·구독 전환 성공률·커버리지% 등 제품 지표 출처 없음. D1-860의 -39%/-80%는 **테스트 실행시간**이라 제품 성과로 쓰면 과장. 제품 지표를 기억으로라도 댈 수 있나? [측정필요]
2. **`imago-payment-client` 본인 단독 vs 공동?** SUB-331/387 커밋은 본인이나, 앱 초기 스캐폴딩·전체 결제 플로우 설계가 본인 단독인지 팀 공동인지 확인 필요. [기여확인]
3. **BO 구독 E2E "대량 작성"의 본인 단독성**: D1-8xx 다수가 본인 assignee이나, 같은 스위트에 다른 작성자 기여가 섞였는지(공동 작성 여부) 확인 필요. [기여확인]
4. **DealerChecker 설계 주체**: 렌더프롭 가드 구조를 본인이 설계했는지, 팀 패턴을 따른 것인지. [기억]
5. **테스트 파일 33개 vs 기존 32개**: 시점·집계 기준 차이(구 레포 vs 현 src)인지 단순 증가인지 — 이력서엔 모호 수치 대신 "구독/시트/빌링 도메인 단위·통합 테스트"로 정성 기술 권장. [측정필요]
6. **SUB-407 폴링 한도가 푼 실제 증상**: 무한 폴링이 사용자에게 어떤 문제(브라우저 부하/배터리/서버 부하)였는지 기억으로 보강 가능한가. [기억]

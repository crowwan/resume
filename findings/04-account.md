# 04. DentBird Account — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: done (코드·Confluence 발굴 + 4축 인터뷰 완료. 정량 지표는 부재 — 설계 판단·성장 서사 중심 카드)
> 발굴 대상: `~/Works/devops/dentbird-account-client`(구 레포), `~/Works/devops/dentbird-solutions/apps/account-client`(마이그레이션 후), Confluence(본인 작성 문서 7건), vault
>
> **핵심 맥락**: 크레딧 기반 → **구독제(subscription) 전환**의 FE를 담당. 동시에 이 프로젝트가 **본인이 FE 아키텍처·디자인패턴·테스트·에러처리를 본격적으로 학습·실험한 무대**. 결론적으로 "패턴·설계는 팀 전체의 공통 이해가 전제되지 않으면 복잡도만 높인다"는 판단 기준을 얻음(→ 성장 서사 핵심).

---

## 1. 의사결정 흔적

### 아키텍처 — FSD 도입 검토·규칙 정립 (본인 주도)
- **FSD 도입 이유**: layered 폴더 구조의 한계 — 응집도 낮고 결합도 높음, 요구사항 변경 시 어느 파일을 고쳐야/사이드이펙트가 어디 생길지 장담 불가, 도메인 정의 부재로 문제 구조 파악 어려움, 앱 서비스 로직과 비즈니스 로직 혼재, 한 폴더에 파일 과다. [Confluence: page/1459978314 "[작성 중] FSD", 본인 작성]
- **FSD 레이어별 규칙을 직접 설계**: API는 `{Domain}API` 객체 메서드(역할 기반 네이밍), react-query `query key factory`, **DTO↔도메인 객체를 mapper로 분리**(요청 직전/응답 직후 변환), custom hook 위치를 3안 비교 후 "UI 조작(도메인 무관) 코드는 shared" 로 결정. [Confluence: page/1465942062 "FSD 레이어별 정리 제안"]

### 컴포넌트 디자인 패턴 — 실험하며 트레이드오프까지 기록
- **Compound Component 패턴**: MobileHeader를 실험 대상으로 도입. 장점(props 의존 제거, context 접근 제한으로 다른 관심사 컴포넌트와 데이터 격리) / 단점(Context API 리렌더·세팅 비용)을 **본인이 직접 양면 기록**. [Confluence: page/993493040]
- **Render Props 패턴**: `AdditionalSeatsDialogue`/`SeatCounter`에 적용 — 관심사 분리 + 외부에서 내부 데이터(seatCount) 사용 가능. **단, "커스텀훅+props로도 같은 기능 가능"이라는 트레이드오프를 명시**(과설계 경계). [Confluence: page/1010401361]

### 에러 처리 — 선언적 전환 + Fault Tolerance 설계
- **문제 인식**: 제대로 된 에러 처리 부재 — 모든 에러를 공통 unknown 처리, 한 기능 에러가 전체 페이지를 에러화(케이스 리스트 조회 실패 → SNB·대시보드까지 접근 불가). [Confluence: page/1140457769]
- **선언적 에러 처리(ErrorBoundary)**: 명령형 `if (!data) return null` 방어 코드를 ErrorBoundary로 이관, 컴포넌트가 "데이터 없음" 상황을 신경 쓰지 않게. **Fault Tolerance**(어디까지 장애 허용? billing 정보 못 받아도 payment history·구독취소·뒤로가기는 살린다)를 도메인 의존 관계 기준으로 직접 구분. [Confluence: page/1140457769]
- **Business Logic Error 커스텀 처리 설계**: 서버가 `statusCode:200, success:false, errorCode` 형태로 내리는 비즈니스 에러를 `BusinessLogicError` 클래스로 throw, `BusinessLogicErrorHandler`의 `.when(errorCode, action).otherwise()` 체인으로 에러코드별 분기 처리. [Confluence: page/1268383861]

### 테스트 — 도입 동기와 전략
- **TDD 맹신 아님**: "테스트 코드 ≠ 반드시 TDD. 목적에 맞게." 선작성/요구사항 먼저/후작성을 상황별로 선택. **테스트가 부담·역효과면 지금 작성이 맞는지 재고**하라고까지 기록. [Confluence: page/1095270427]
- 전략: `describe-context-it` 구조화, `msw`로 서버 응답 모킹, setup/teardown(beforeEach/afterEach)로 테스트 독립성. [Confluence: page/1095270427]

---

## 2. 측정 가능한 변화

- **테스트 코드 32개** (구 레포 `src` 기준, `*.test.*`/`*.spec.*`). 구독/시트/빌링 도메인에 집중(UpgradePlan·AdditionalSeats·Coupon·MyPlan·MemberList·CreditHistory·CancelSubscription 등). [code: dentbird-account-client/src]
- **패턴 적용 범위(before→after)**: 구 레포는 `src/App/Subscription/` 모듈만 부분 FSD(나머지는 layered: Components/Hooks/Apis/Pages…), 마이그레이션 후 `solutions/apps/account-client`는 **전면 FSD**(features/shared/pages/layouts/lib). [code: 두 레포 비교]
- 정량 성과 지표(커버리지%, 버그 감소율 등)는 **출처 없음**. [측정필요]

---

## 3. 후속 영향

### 마이그레이션 (구 레포 → 모노레포)
- `dentbird-account-client`(독립, craco+jest, 부분 FSD) → `dentbird-solutions/apps/account-client`(NX 모노레포, **Vite+vitest**, **전면 FSD**)로 통합. dev 포트 9000, `/accounts/` 경로. [code: solutions/apps/account-client/project.json, DEVELOPMENT.md]
- 설계 자산이 그대로 이어짐: `BusinessLogicError`/`BusinessLogicErrorHandler`(`shared/class/Error.ts`), 페이지 단위 `withErrorBoundary` HOC, `mswSubscriptionHandlers`(시트/구독/결제/딜러십). [code: solutions/apps/account-client/src/App/Subscription/]
- 단, account-client는 모노레포에서 **임베드 대상이 아니라 호스트 앱**으로 유지(임베드 대상은 setting/explorer/viewer/export 모듈). [vault: projects/dentbird-solutions/decisions/module-migration.md]

### ★ 성장 서사 (가장 중요 — 인터뷰로 구체화 완료, 2026-06-02)
디자인 패턴·DDD·FSD를 학습·도입해 봤으나, **"기술 도입은 문제 해결이 우선"**이라는 판단 기준을 얻음. 구체 깨달음(사용자 원문 취지):
- **FSD: 폴더 위치 기준이 불명확**했다. "어떤 파일을 만들 때 위치 고민 없이 만들 수 있어야 좋은 구조"인데, 실제로는 **매번 어디에 넣을지 상의하는 시간이 많았다**. [기억]
- **디자인 패턴: 안 써도 되는 곳에 "코드 일관성"을 이유로 적용**한 게 많았다. 본인이 제안한 방향을 **팀원이 잘못 이해해, 같은 패턴인데 쓰는 방법이 달라지는** 일이 잦았다. [기억]
- **핵심 통찰**: 디자인 패턴은 *"오랜 기간 내려온 공통 문제를 해결하는 패턴"*인데, 본인은 **그 패턴이 풀려는 문제를 이해하지 못한 채 도입**했다("일관되지 않은 코드 구조"를 명분으로 삼았지만 그건 패턴이 푸는 문제가 아니었다). → 기술 도입은 **우선순위 높은 문제 해결이 먼저**고, 패턴은 그 수단일 뿐. [기억]
- 단, "좋은 경험이었고 좋은 형태의 코드라는 생각은 지금도 변함없다" — 구조 자체를 부정하는 게 아니라 **도입 목적·팀 공통 이해의 전제**를 배운 것. (실제로 패턴·FSD는 마이그레이션 후 모노레포에 그대로 정착 → §3 마이그레이션) [기억]
- **이 프로젝트에서 테스트 코드의 필요성을 처음 체감 → 단위·통합 테스트 도입을 시도**. (이후 카드의 E2E/테스트 자산으로 연결되는 출발점) [기억] + [Confluence: page/1095270427]

---

## 4. 운영 패턴

- account/구독 도메인을 **장기 전담**: 첫 커밋 2023-09-14 ~ 마지막 2025-07-24, **사용자 커밋 156개**. (레포 자체는 2022-08 생성 — 본인 합류 전부터 존재) [git: dentbird-account-client, --author 집계]
- **비즈니스 모델 전환(크레딧 → 구독제) FE 대응**: 플랜 업그레이드(`upgrade-estimate`), 시트 구매(`seats/buy`, `seats/purchase-estimate`), 구독 취소/재개(`cancel`, `stop-pending-cancellation`), 쿠폰, 결제수단(customer-portal), 결제 히스토리. 크레딧 레거시(`CreditHistory`, `DeleteAccountWithPaidCredits`)와 공존 — "구독 플랜 도입 이전 사용자용" 문구 잔존. [code: dentbird-account-client/.../shared/constants/apiPath.ts, features/paymentHistory]

---

## 확정된 사항 (2026-06-02 인터뷰)

- **구독제 전환 = 본인 전담 설계·구현 확정**. (크레딧→구독제 신규 기능: 플랜 업그레이드·시트 구매 등) [기억 확정]
- **마이그레이션은 카드化하지 않음** — 구 레포 → solutions 모노레포 이관은 **팀 단위로 진행**됐고, 본인도 **여러 앱을 마이그레이션**함. 세부는 기억 흐림. → 별도 카드 X, **"여러 앱 모노레포 마이그레이션 경험" 한 줄** 수준으로만 (99-misc 또는 Solutions 카드에 흡수 검토). [기억 확정]
- **FSD/패턴 도입의 팀 수용도 = 성장 서사의 실제 근거 확보**: 폴더 위치 상의 시간 과다, 같은 패턴의 사용법 분기(팀원 이해 차) — §3 ★ 참조. [기억]

## 남은 미해결

- **정량 지표 부재**: 테스트 커버리지, 버그/에러 빈도 변화 등 측정값 없음 → 이 카드는 **정량보다 "설계 판단·성장 서사" 중심**으로 간다. [측정필요]
- 디자인 패턴 문서들은 space 1586102525(개인/기술 스페이스 추정) + FSD 문서는 space 352649850. 팀 공유 범위 확인 시 활용. [Confluence]

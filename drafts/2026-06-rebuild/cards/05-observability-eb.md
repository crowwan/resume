**관측성·ErrorBoundary 표준 정렬** — 5개 앱의 3-layer ErrorBoundary를 팀 Datadog 관측 표준에 정렬
`2026.04 ~ 현재` · EB→Datadog 정렬 종합 계획서 author 본인, 3-layer EB 아키텍처·5앱 통합 주도 (관측 어휘 정의는 팀, 본인은 EB 라이브러리를 그 어휘에 정렬)

에러는 잡히지만 어디서 왜 났는지 추적이 안 되던 상태였습니다. 같은 안전망이 모든 실패를 한 메시지로 흡수해 원인 파악이 막혔고, 앱마다 ErrorBoundary와 관측 부착 방식이 제각각이었습니다. 5개 앱의 ErrorBoundary를 팀의 Datadog 관측 표준에 정렬하는 종합 계획을 author로 세우고, 그 과정에서 직접 만든 분류 자산을 스스로 폐기하며 팀 범용 표준에 수렴시켰습니다.

▸ 기여
- 3-layer ErrorBoundary 아키텍처(Root→Section→Feature) 설계·확립, 5개 앱에 통합 *(cloud-desktop·cloud-mobile·account·batch-web 완전 + crown 부분)*
- EB→Datadog 표준 정렬 종합 계획서를 author로 작성·주도 *(의사결정 7차 개정 + 5개 PR 분해 + EB↔Datadog 책임분리)*
- 표준 미적용으로 남아 있던 마지막 앱(batch-web)의 RUM 계측을 전면 재구성해 5앱 정렬 완성
- Export 실패 가시성 ADR 의사결정 *(모든 실패가 한 에러 메시지로 collapse되던 124건을, 커스텀 에러 클래스 + RUM 직렬화로 root cause 추적 가능하게)*
- 최상위 Fallback에 디자인시스템·Hook·i18n 의존을 두지 않는 zero-dependency 원칙 확립 *(안전망이 자기 의존성 때문에 죽지 않도록)*

▸ 판단·지속성
- 직접 추가했던 에러 분류 체계(Taxonomy 7분류)가 특정 앱에 편향돼 있다고 판단 → 팀 전체가 범용으로 쓸 규칙이 필요하다고 보고, 팀 Datadog 가시성 문서의 표준에 맞춰 **자기 분류를 스스로 폐기·정렬**(자기 작업의 비판적 축소)
- ErrorBoundary가 비즈니스 도메인을 자동 부여하지 않도록 설계 — 자동 경로는 발생 위치만 표기하고, 도메인 분류는 호출자가 명시. 안전망과 의미부여의 책임을 분리
- 지속성: 관측 어휘 표준 자체는 팀이 정의했고, 본인은 EB 라이브러리를 그 어휘에 정렬하는 프론트 구현체를 맡았습니다. 미정렬 앱이 추가되면 같은 정렬을 적용하는 방식으로 운영 중

`기술` React · TypeScript · ErrorBoundary · Datadog RUM · 모노레포(NX)

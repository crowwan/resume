관측성·ErrorBoundary — 5개 앱을 팀 Datadog 표준에 정렬
2026.04 ~ 현재 · EB→Datadog 정렬 종합 계획 author·5앱 통합 단독 주도
에러는 잡히지만 어디서 왜 났는지 추적되지 않던 상태. 같은 안전망이 모든 실패를 한 메시지로 흡수했고, 앱마다 ErrorBoundary와 관측 부착 방식이 제각각이었음.
- 3-layer ErrorBoundary 아키텍처(Root→Section→Feature) 설계·확립, 5개 앱 통합 — cloud-desktop·cloud-mobile·account·batch-web 완전, crown 부분
- EB→Datadog 표준 정렬 종합 계획을 author로 주도 — 의사결정 7차 개정·5개 PR 분해·EB↔Datadog 책임 분리
- 모든 실패가 한 에러 메시지로 collapse되던 Export 실패 124건을, 커스텀 에러 클래스와 RUM 직렬화로 root cause까지 추적 가능하게 재구성
- 최상위 Fallback에 디자인시스템·Hook·다국어 의존을 두지 않는 zero-dependency 원칙 확립 — 안전망이 자기 의존성 때문에 죽지 않도록
- 안전망은 발생 위치만 자동 표기하고 도메인 분류는 호출자가 명시하도록 분리 — 안전망과 의미부여의 책임을 떼어냄
- 직접 추가했던 7분류 에러 체계가 특정 앱에 편향됐다고 보고, 팀 Datadog 가시성 표준에 수렴시키며 자기 분류를 스스로 폐기 — 관측 어휘 표준은 팀 정의, 본인은 EB를 그 어휘에 정렬

기술 React · TypeScript · ErrorBoundary · Datadog RUM · 모노레포(NX)

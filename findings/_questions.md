# _ 발굴 후 확인 질문 모음 (사용자 인터뷰 대기)

> 전체 스페이스·코드 발굴 후 남은 `[기여확인]`/`[기억]` 항목. **이력서 정확성·발명 방지의 핵심.** 사용자가 답하는 대로 각 findings에 반영.

## 🔴 1순위 — 기여 주체 (이력서에 박을지 결정)

1. **빌드 최적화(CI 456s→40s, -91%, qa/prod 배포 120s→11s)**: 본인 기여가 있나? 있다면 어느 부분(shared node_modules snapshot / Vite·Rspack 전환 / GitHub Actions·NX affected / Build Once)? 아니면 팀 작업이고 본인은 무관? → findings/_service-overview §3, 01
2. **JWT→Redis 세션 전환**: 사용자가 "JWT→session cookie 전환을 진행했다"고 함. 본인이 **FE 클라이언트 인증 전환**(토큰→쿠키, 세션 폴링, IdleTracker)을 담당했나, BE팀(김현철 등) 작업을 FE에서 연동만 했나, 거의 무관인가? → findings/10-B
3. **파일 AEAD 암호화(KMS envelope)**: 사용자가 "암복호화 진행 중"이라 함. 본인 직접 구현/검증 범위는? (격리 e2e 검증 외 FE decrypt 연동 등) → findings/10-A
4. **AI 도구 리딩(Claude Code 핸즈온·dentbird-bot·CLAUDE.md 개선·Batch CLI AI·PR Complexity)**: Confluence 작성자 **전부 김현철(hckim)**. 본인은 공동 참여/영향인가, 단순 참석자·사용자인가, 무관인가? (TS 챌린지 스터디 참여 여부 포함) → findings/_service-overview §11
5. **iwtk→Three.js**: 본인 24커밋이 Viewer 색상/abutment 렌더 수정 중심. 그 외 cloud-mesh-io 설계·계획 참여가 있었나? → findings/08
6. **디자인 시스템**: DatePicker 확장(10커밋) 외에 테마/토큰/마이그레이션 주도 같은 더 큰 범위가 있었나? → findings/07
7. **GA4 전환 측정(Landing→Account attribution)**: 권장 구조 제안/구현이 본인인가? 서버 Measurement Protocol 구현 범위? → findings/_service-overview §8
7b. **★ 배포/릴리스 신체계(GitHub Actions 앱별 독립 배포·Build Once·호환성 경계·ECR 태그)**: git author가 jwkim으로 잡힘 — 본인이 이 배포 파이프라인을 설계·구현했나? 어느 범위(클라/서버/호환성 경계)? 03 MFE "통합 배포 전략"과 같은 작업인가? → findings/_service-overview §15 [최우선 확인]

## 🟡 2순위 — 효과/사례 (정성 보강, 면접 답변용)
8. **격리환경 커밋 재현**이 실제로 잡은 버그/상황 1~2개. → 01
9. **Claude 자동 E2E** 실제 운영 기간 + 그동안 잡은 회귀 사례. → 09
10. **E2E 본격 도입** 전/후 체감(수동 QA 부담 감소 등) + 본인이 그 시점에 주도한 계기. → 09
11. **디자인 시스템** 도입으로 Design QA 실제 감소 체감. → 07
12. **런타임 config** CI 빌드시간 "3배" 정확 수치(또는 정성). → 01

## 🟢 3순위 — 범위/정정 확인
13. Account: 크레딧→구독제에서 결제 연동(Stripe customer-portal 등) 본인 범위. → 04
14. 멀티테넌트: Account FE에서 테넌트 배정/`?tid=` 처리 본인 담당분. → _service-overview §9
15. 빌드 최적화의 prefetch viewer: 본인 작업인가 팀인가. → _service-overview §3

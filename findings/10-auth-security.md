# 10. 인증/보안 — 파일 암호화(AEAD) + JWT→Redis 세션 — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: done(발굴) / ⚠️ **독립 개인 카드 아님** — 본인 기여는 격리 AEAD 검증(→09)뿐, 인증은 서비스 이해용. (2026-06-03 확정)
> 발굴 대상: `dentbird-solutions` platform-server/api-gateway, Confluence(인덱스 A·B), vault
>
> **★ 기여 주체 주의**: 두 주제 모두 **핵심 구현은 백엔드 팀 주도**(김현철·Jinyong Lee 등). 사용자는 "JWT→session cookie 전환을 진행했다"고 했고 파일 암복호화도 "진행 중"이라 했으나, git author 매칭은 불명확 → **본인 FE측 기여/검증 범위를 인터뷰로 확정 필요.** 발명 금지.

---

## A. 파일 스토리지 S3 전환 + AEAD 암호화 (KMS envelope AES-256-GCM)

### 1. 의사결정 흔적
- **XOR 마스킹 폐지 → AEAD 도입**: XOR(`^0xff`)는 가역이라 기밀성 아님 → 진짜 보호 위해 **KMS envelope + AES-256-GCM**. [Confluence: page/2391605278]
- **Workspace × Region × UTC-day 단위 dataKey 재사용**: KMS Decrypt 부하 축소(per-page **92회 → 1~3회**), blast radius는 "하루치"로 명시적 트레이드오프. [git: commit bc2b7dad8c1, D1-5325]
- **단일 태그 AES-GCM V1 → (미래) Framed V2**: prod 미노출 창에 streaming/메모리 효율 업그레이드. [Confluence: page/2416345097]
- **모델**: `FileProtectionClass`/`FilePayloadEncoding`/`FileArtifactKind`로 파일군별 보호 정책. [Confluence: page/2405630299]

### 2. 측정 가능한 변화
- 암호화 방식 XOR 마스킹 → AES-256-GCM(128B tag, KMS-wrapped key). KMS 호출 92→~3회(≈760ms→100ms 추정). [git: bc2b7dad8c1]
- 환경 활성화 dev/qa/test/prod 순차(`aead-envelope-enabled: true`, D1-5485). [code: application-{env}.yml]

### 3. 후속 영향
- 업로드 SDK keyPackage 분기로 호출부 무변경(하위호환). Global Library(.drc/.stl/.ply/.obj/.dcm/.vtp) AEAD 승격, Thumbnail은 plaintext 유지. OpenAPI 다운로드는 platform-server server-side decrypt. [Confluence/code]
- 격리(e2e) 환경: dev flag 비활성이면 grant가 PLAINTEXT만 → dev 바이너리 AEAD ciphertext 수동 decrypt(9-step fixture seed). [vault: learnings/2026-05-27-aead-envelope-isolated-self-seed.md]

### 4. 기여 주체
- **[팀]** 핵심 구현(D1-5325/5485/5457 등, git author 본인 매칭 불명확). 코드: `AeadEnvelopeEncryptionService.kt`, `ProtectedArtifactKeyPackageService.kt`, `WorkspaceEncryptionKeyService.kt`, `FileProtectionPolicy.kt`(platform-server), `libs/platform-api-client/.../io.ts`(client decrypt).
- **[본인 vault]** DEN-3706 격리 e2e fixture 구축 중 AEAD decrypt 파이프라인 전수 검증(AES-256-GCM, gzip magic 정합). [vault]

---

## B. 인증/세션 — JWT → Redis 세션 전환

### 1. 의사결정 흔적
- **JWT(localStorage) → HttpOnly 쿠키(sessionId) + Redis 세션**. 동기: ① XSS 토큰 탈취 방지(localStorage→HttpOnly), ② 서버 측 즉시 강제 로그아웃(Redis DEL), ③ API 요청 단계 4~7 → 3단계. [Confluence: page/2177794115]
- **크로스리전**: 리전별 독립 Redis + 홈리전에만 세션 생성. 타리전 로그인 시 **임시 JWT(30초 TTL)** 중계 + CloudFront `x-imago-region` 라우팅. BO는 KR Redis 세션 위임(프록시 세션 30분). [Confluence: page/2169569324]
- **쿠키 SSO**: `Domain=.dentbird.com; SameSite=Lax; HttpOnly`로 서브도메인 자동 인증. [Confluence]

### 2. 측정 가능한 변화 / 트레이드오프
- 요청 단계 감소(JWT refresh·Mongo 조회 제거 → Redis HGETALL). 다중 로그인 제어 Mongo검색 → Redis 인덱스. [Confluence §3]
- 트레이드오프: Redis 의존(장애 시 신규 세션 불가, JWT fallback), 크로스리전 복잡성, 서드파티 쿠키 정책(Safari ITP/SameSite). [Confluence §4]
- 클라이언트 `IdleTracker`(localStorage 타이머) vs 서버 truth 불일치 → 세션 만료 QA 이슈 다수(분석상 34%). [Confluence: page/2251161860]

### 3. 기여 주체
- **[팀]** 백엔드 주도: `860d9b94994`(2026-02-03 Hyeoncheol Kim "JWT→Redis 세션 Phase 0-1"), `e40283bf906`(legacy-batch 세션 쿠키 전환), D1-3398(Jinyong Lee 크로스탭). 코드: `SessionVerificationFilter.kt`, `GatewaySessionReader.kt`, `RegionDirectRouter.kt`, `CrossRegionLoginTokenService.kt`.
- **[본인?]** FE측 쿠키 기반 인증 전환(axios 토큰→쿠키, IdleTracker 등) 기여 가능성 — **확인 필요**. account-client `AxiosAuthInstance` 등. [기여확인]

## ★ 확정 (2026-06-03 인터뷰)
- **JWT→Redis 세션**: 본인 **거의 무관**(BE팀 주도). → 인증(B)은 **개인 카드 아님**, [_service-overview §5/6](./_service-overview.md)(서비스 이해)에만 둔다.
- **파일 AEAD 암호화**: 본인은 **격리 e2e 환경에서 AEAD decrypt 전수 검증(DEN-3706, fixture seed)만**. 구현은 BE팀. → **09 테스트/격리재현에 "격리 환경 AEAD decrypt 검증" bullet로 흡수.**
- **결론: 10은 독립 개인 카드에서 제외.** 보안/인증은 "팀이 이렇게 했고 나는 이해한다"(서비스개요), 본인 손이 닿은 건 격리검증(09)뿐. 이 파일은 **서비스 이해용 사실 보관**으로 유지.

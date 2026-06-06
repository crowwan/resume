# 인증/보안 (AEAD 파일암호화·JWT→Redis 세션) — 본인 기여 범위 검증 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 기존 `findings/10-auth-security.md` 와 중복분 제외, NEW/보강만 적재

## 본인 기여 식별 요약

- **코어(AEAD/KMS/JWT→Redis)는 BE팀.** monorepo(`dentbird-platform-server-monorepo`) 기준 jwkim 인증/보안 코어 커밋 0건. crypto 서비스 3종(`AeadEnvelopeEncryptionService.kt`/`KmsEnvelopeKeyWrappingService.kt`/`ProtectedEncryptedArtifactBackfillService.kt`)에 jwkim 비-머지 line 변경 0건(잡힌 건 21,285 files 일괄 임포트 부수효과). [git: count=0; git: e5af5c6dcd1]
- **문서 author 레벨에서도 코어 = BE팀 재확인**: AEAD 상세 3문서 author=Jinyong Lee(2391605278/2405630299/2412740622), JWT→Redis 문서 author=Hyeoncheol Kim(2177794115/2251161860). 본인 작성 문서 아님. [confluence: author 필드]
- **본인 슬라이스 = FE 소비측 안정화.** BE팀 세션쿠키/AEAD 코어 위에서 FE가 겪는 회귀·UX·vendor OAuth race를 수습·추상화. 기존 findings에서 "[기여확인] 확인 필요"였던 FE 인증 follow-up들이 이번에 git author + vault로 **본인 확정**.
- **모호 없음(확정)**: 아래 1-A/1-B 항목 전부 git author=jwkim 또는 vault 본인 작성으로 교차확정. 단 정량 효과는 [측정필요].

## 1. 의사결정 흔적

### 1-A. 본인 FE측 결정 — 커밋 (기존 findings에 없던 항목만)
- ★ **AuthService.overrideSessionHandlers 추상화 — 4개 모듈 통합**. [git: cceffb6045b, 26b29a8db9e]
- ★ **embed-modules에서 overrideSessionHandlers 제거**. [git: 8c751728b2c / PR 25776]
- ★ **IdleTracker mis-fire 3단 방어**(stopIdleTracking → noop subscribe → restartIdleTracking). [git: 2977035c352 — DBO-1226/DEN-4526/DEN-4527]
- ★ **Export Session JWT→세션쿠키 포워딩 전환**(SessionCookieFeignConfig). [git: f30e7274fa6 / PR 30326]
- ★ **Phase 0 Unified-Domain CookieUtil 리팩토링**(getDomain 18줄→6줄). [git: 70ab6689d8c]
- ★ **batch P004 OIDC 로그인 sessionStorage 트램펄린 판별**. [git: 584075d454e]

### 1-B. ★ NEW — vault 발굴 본인 FE 인증 결정 (커밋과 별개 증거, 기존 findings 전무)
- ★ **vendor OAuth axios interceptor "request-level priority guard" 결정(WV-002, MeditLink)**: module-level `currentAccessToken` 이 호출자 명시 Authorization 헤더를 무조건 override → stale token 401. fix 정공 = `!config.headers['Authorization']` guard(request-level 우선) + logout 시 instance state cleanup. interceptor 의도(default fallback) vs 코드(무조건 override)의 결함을 본인이 식별·교정. [vault: learnings/2026-05-22-axios-interceptor-request-level-priority-and-state-cleanup.md · PR #10067] [기억]
- ★ **CRWN-3489 Shining3D OAuth 콜백 구조 재설계 결정**: 1차(useRef 멱등+useCallOnce) 머지 후 dev 재발 → 2차로 **콜백 페이지를 부트스트랩 트리(AuthHoc→WorkspaceWorkersProvider) 밖 경량 shell 로 분기**(remount 원천 제거)로 근본 해결. legacy iframe explorer(`module-explorer.dentbird.com`) 호환 제약까지 고려한 Parallel Change 판단. [vault: bugs/CRWN-3489/investigation.md · PR #10471→#10483(merged 60c0efb)]
- ★ **"vendor OAuth bug는 격리 E2E 가 아니라 unit test 가 정공" 진단 룰 정립**: medit-stub 이 patchMeditInfo 를 모사하지 않아 "격리=production fault 정확 모사"라는 반례를 dev 검증으로 직접 발견 → 잘못된 learning 을 `status: invalidated` 로 박제, decision 으로 정립. [vault: learnings/2026-05-22-vendor-oauth-stub-natural-healing.md(invalidated) + decisions/2026-05-22-isolated-stub-faithful-to-prod-fault-by-default.md] [기억]

### 1-C. [팀] BE팀 코어 — 보강분만 (서비스 이해용, 본인 무관 — 기존 findings B/A 항목과 중복분 제외)
- [팀] TTL 세부: AT 15분 / RT 24시간 → **세션 30일(활동 시 리셋)**. 앱별 쿠키 분리(`sessionId`/`boSessionId`/`batchSessionId`). 임시 JWT **30초 TTL** 중계. [confluence: 2177794115 — author Hyeoncheol Kim]
- [팀] AEAD 클라이언트 측 WebCrypto encrypt(`extractable=false`), wrappedKey 응답 제외, 키 3단 계층(CMK→dataKey→wrappedKey). [confluence: 2412740622 — author Jinyong Lee]
- [팀] **D1-5457(2026-06-04)**: `STANDARD_USER_DATA`(환자 scan mesh 포함) 전부 **PLAINTEXT 전환**, AEAD 는 `PROTECTED_DESIGN_ORIGINAL`/`PROTECTED_LIBRARY_ORIGINAL` mesh 한정. [confluence: 2391605278, 2405630299]

## 2. 측정 가능한 변화   (출처에 적힌 수치만)

### 2-A. 본인 작업 효과 (커밋 본문 명시값)
- ★ **overrideSessionHandlers 줄 감소**: explorer 14줄→1줄, export 20줄→1줄, setting 14줄→1줄, viewer 14줄→1줄. [git: 26b29a8db9e]
- ★ **CookieUtil.getDomain() 18줄→6줄(67% 감소)**. [git: 70ab6689d8c]
- ★ **DEN-4583 마스킹 binary leak 회귀 가드 fixture**: `plain/crown.drc.gz`(8800 bytes, gzip magic `1f 8b`) vs `masked/crown.drc.gz`(8800 bytes, magic `e0 74`=XOR 0xFF) 쌍. (레거시 XOR 0xFF 마스킹 검증, AEAD 아님). [git: 92d99f17c8e / PR 9670]

### 2-B. ★ [팀] Confluence 분석치 — 본인 작업 효과 아님 (혼동 금지 / 이력서 인용 주의)
- ★ **"세션 만료/비활동 감지 QA 이슈 = 인증 이슈 50건 중 34%(~17건)"** — IdleTracker 클라이언트 타이머 vs 서버 truth 불일치의 구조적 문제. **이건 Hyeoncheol Kim 의 분석 수치(분석일 2026-04-06)이지 본인 작업의 개선 효과가 아니다.** 본인은 그 34% 카테고리 내 개별 티켓 일부를 FE측에서 수습. [confluence: 2251161860]
- [팀] AEAD KMS Decrypt/list page: **92회→1~3회**(per-object dataKey→workspace×region×UTC-day 재사용), **760ms(응답 22~43%)→감소**. [confluence: 2412740622 / D1-5325] (기존 findings 10번에도 92→1-3 기재됨 — 760ms/22~43% 만 보강)
- [팀] Redis 조회 레이턴시 **<1ms** vs MongoDB 수~수십ms, API 요청 **4~7단계→항상 3단계**, 로그인 플로우 **12단계→7단계**. [confluence: 2177794115]
- (참고) dentbird-solutions tag **1,710개** — nx 패키지별 릴리스라 "인증 릴리스 개수"로 환원 불가. [git tag]

## 3. 후속 영향

- ★ **Export Session 쿠키 포워딩 전환 → platform-server BFF 파급**(`ImagoFileFeignClient`/`ExportSessionService`/`Controller`). [git: f30e7274fa6]
- ★ **IdleTracker fix → account-client + dealer-backoffice-client 양쪽 적용**, D1-3398 회귀(BO `/users/me` 누락→400) `/back-office/admins/me` 분기 + BO 재로그인 루프 수정. [git: 9c6ab7350b8 — D1-3398]
- ★ **세션 만료 UX**: `RefreshTokenInvalid` 없는 i18n 키 → `session_expired_dialog` + **11개 언어 번역**, 버튼 "Refresh"→"OK". [git: 03421a7df47 — auth-react-sdk]
- ★ **NEW — IdleTracker fix 대상 QA 5티켓이 본인(assignee=currentUser) 이슈로 확정**: DEN-4519(My Designs 4시간 대기 시 Session expired 모달 미발생→Unknown error), DEN-4520(Dashboard 모달 미노출+흰 화면), DEN-4526(모달 중복 노출), DEN-4527(미로그인 상태 모달 오노출), DBO-1226(BO 미활동 흰 화면) — **5건 모두 종료(closed)**, 생성 2026-03-30~31. → 본인 IdleTracker 3단 방어가 이 5개 QA 티켓을 닫은 작업임을 Jira assignee 로 교차확정. [jira: DEN-4519, DEN-4520, DEN-4526, DEN-4527, DBO-1226 (assignee=currentUser, status=종료)]
- ★ **NEW — DEN-3706 격리 AEAD decrypt 9-step 자가시드 파이프라인 상세**: dev S3 ciphertext(`AEAD_ENVELOPE_V1`=AES-256-GCM, `APPLICATION_ENCRYPTED`=XOR 0xff)를 격리 minio 용으로 AES-256-GCM decrypt + gzip magic `1f 8b`/jpeg `ff d8` 정합 검증 후 시드. 산출물 `dev/active/den-3706-implant-library-viewer/`(decrypt.mjs 포함) 영구 보존. **본인이 crypto 를 구현한 게 아니라, BE팀 AEAD ciphertext 를 격리에서 복호화·검증하는 파이프라인을 운영.** [vault: learnings/2026-05-27-aead-envelope-isolated-self-seed.md · bugs/DEN-3706] (기존 findings 10번엔 "9-step fixture seed" 한 줄만 — V1/encoding/magic 검증 상세가 NEW)

## 4. 운영 패턴

- ★ **본인 인증 작업 = 일관되게 "BE팀 JWT→Redis 세션/쿠키·AEAD 코어 위에서 FE 가 겪는 회귀·UX 버그 수습·추상화"**. (D1-3398 BO 회귀 / 세션쿠키 마이그 후 Export NPE / IdleTracker 미인증 mis-fire / 세션만료 모달 i18n / vendor OAuth interceptor·콜백 race). 코어 설계 아님 = **소비측 안정화가 본인 슬라이스.** [git: 2977035c352, 9c6ab7350b8, f30e7274fa6, 03421a7df47; vault: WV-002, CRWN-3489]
- ★ **NEW — vendor OAuth(MeditLink/Shining3D) 클라이언트 인증 결함을 unit test 정공으로 박제하는 운영 패턴**: 격리 stub 자연치유 가설을 dev 검증으로 반증하고 진단 룰을 vault decision 으로 정립. axios adapter capture 로 interceptor 동작을 unit level 에서 RED→GREEN. [vault: axios-interceptor learning, isolated-stub-faithful decision]
- ★ **DEN-4583**: XOR 0xFF 마스킹 binary leak 을 격리(docker mongo+MinIO)에서 회귀 가드로 박제 — 09(테스트/격리재현)와 동일 패턴. [git: 92d99f17c8e / PR 9670]

## ★ 기존 findings/이력서에 없던 새 증거

1. **WV-002 axios interceptor request-level priority guard 결정** — interceptor default-fallback 의도 vs 무조건 override 코드 결함을 식별·교정 + logout state cleanup. [vault · PR #10067] [기억]
2. **CRWN-3489 OAuth 콜백 트리 외 경량 shell 분리 재설계** — 1차 멱등 패치 dev 재발 → 부트스트랩 트리 밖 분기로 remount 원천 제거(Parallel Change). [vault · PR #10471→#10483]
3. **"vendor OAuth = unit test 정공" 진단 룰 정립** — 잘못된 learning 을 invalidate, decision 으로 박제. [vault]
4. **IdleTracker fix 대상 5개 QA 티켓(DEN-4519/4520/4526/4527, DBO-1226) 본인 assignee + 전부 종료** — Jira 교차확정. (기존 findings 엔 IdleTracker 만 언급, 티켓 연결 없었음) [jira]
5. **DEN-3706 격리 AEAD decrypt 9-step 파이프라인 상세** — V1=AES-256-GCM / APPLICATION_ENCRYPTED=XOR 0xff 분기, gzip/jpeg magic 정합 검증, decrypt.mjs 영구 보존. [vault]
6. **AEAD 상세 3문서 author=Jinyong Lee / JWT→Redis 문서 author=Hyeoncheol Kim** — 코어 BE 주체를 문서 author 레벨에서 재확인. [confluence: author 필드]
7. **overrideSessionHandlers 4모듈 통합 + 모듈별 줄 감소 수치(14→1, 20→1)** — 기존 findings 엔 없던 본인 추상화 작업. [git: 26b29a8db9e]
8. **Export Session JWT→세션쿠키 포워딩 전환(SessionCookieFeignConfig) + BFF 파급** — 본인 확정. [git: f30e7274fa6 / PR 30326]
9. **세션 만료 i18n `session_expired_dialog` 11개 언어 + 버튼 Refresh→OK** — 본인. [git: 03421a7df47]

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- **[측정필요]** overrideSessionHandlers·IdleTracker 3단 방어가 줄인 실제 세션만료 QA 재발 빈도 — Confluence "34%"는 **본인 효과 아님**(Hyeoncheol Kim 분석치). 본인 fix 의 정량 개선 출처 수치 없음(5개 QA 티켓 close 사실만).
- **[측정필요]** vendor OAuth interceptor guard(WV-002) 이후 401 stale-token 재발 0 여부 — 출처 수치 없음, unit test PASS 만 확인.
- **[기억]** WV-002 / "unit test 정공" 진단 룰 / CRWN-3489 2차 재설계 — vault + 기억 기반. PR 머지·dev 재현 정황은 확정되나, 이력서 인용 전 "내가 단독 진단/설계했는지 vs 팀 리뷰 동반인지" 본인 확인 권장.
- **이력서 격상 판단(재확인)**: 코어 격상 **비권장**. 단 "FE 세션·인증 안정화"가 09(테스트/격리)보다 두꺼워졌으므로, 별도 보조 bullet 로 정직하게 둘 근거 생김. 예: "BE 세션쿠키/AEAD 마이그레이션 후속 FE 회귀·UX 5건+ 수습 — IdleTracker 3단 방어, vendor OAuth interceptor/콜백 race 구조 재설계, Export 쿠키 포워딩". → bullet 채택 여부 사용자 결정 필요.

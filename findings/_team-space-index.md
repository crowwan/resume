# _ 팀 Confluence 스페이스(Dentbird1) 주제 인덱스 — 발굴 로드맵

> 팀 스페이스 `Dentbird1`(spaceId 1589444837) 전체 200 페이지를 주제별로 분류. **발굴 로드맵 + 면접 "서비스 설명" 인덱스.**
> ⚠️ 팀 스페이스라 작성자 다수 — **기여 주체는 발굴 시 git/Confluence author로 확인.** 여기엔 "주제가 존재한다"만 기록.
> fetch는 pageId를 ARI(`ari:cloud:confluence:29dab47b-1317-4b27-9c81-9fee8dc2e724:page/<id>`)로. (조회 2026-06-03)

---

## A. 파일 스토리지 S3 전환 + AEAD 암호화 ★ (→ 10 인증/보안 핵심)
사용자 언급 "파일 접근 권한 암복호화"의 실체. KMS envelope AES-256-GCM. 매우 풍부.
- 2293235738 파일 스토리지 S3 전환 아키텍처
- 2334425171 AWS 인프라 구축 현황 — 파일 스토리지 S3 전환
- 2313420831 AWS S3 파일 스토리지 인프라 구성 보고
- 2331279373 File Storage 아키텍처 — Platform Server 통합
- 2391605278 보호 원본 mesh 암호화 — Azure plaintext → KMS envelope AES-256-GCM
- 2412740622 보호 원본 AEAD Envelope 암호화 — KMS·platform·client 흐름
- 2405630299 S3 파일 보호·권한 모델 (FileProtectionClass/FilePayloadEncoding/FileArtifactKind)
- 2416345097 AEAD 파일 암호화 — 단일 태그 vs Framed
- 2384396302 AEAD_ENVELOPE_V1 — KMS 인프라 요청 (CIRM-366)
- 2328232359 AWS KMS CloudWatch Alarm
- 2408022163 AEAD backfill 동시성 race — mesh 로드 실패 원인·수정·복구
- 2409988107 밀링 글로벌 리소스 mesh 로드 실패 — dedup × per-key AEAD 충돌
- 2333999412 글로벌 리소스 마이그레이션 절차
- 2356707673 / 2357886980 파일 다운로드 마이그레이션 — 후속/효과 분석
- 2340061185 D1-5004 Client file-server API 호출 잔재 분석

## B. 인증/세션 ★ (→ 10 인증/보안)
사용자 언급 "JWT → session cookie 전환"의 실체(= JWT → Redis 세션).
- 2177794115 인증 시스템 전환 요약: JWT → Redis 세션 ★
- 2251161860 인증/세션 아키텍처 분석 및 개선 방향
- 2293825606 api-gateway-server / platform-server 인증 필터 정리 플랜
- 2169569324 크로스리전 로그인 메커니즘 분석
- 2308866089 login-matrix 개선 및 멀티리전 로그인 수정
- 2378727509 prod Google 로그인 unknown error 근본 원인

## C. 빌드/배포 최적화 (→ _service-overview / 01)
사용자 언급 "빌드 시간 최적화".
- 2284322927 Build Once Deploy Everywhere — 클라이언트 배포 CI 최적화 ★(=01 런타임config)
- 2284322880 클라이언트 빌드 시간 최적화 6주 트렌드 분석 (2026-03~04)
- 2221735944 Cloud Desktop CI/빌드 최적화 종합 보고서 (2026-03)
- 2358378605 nx daemon / crown-client cold start 최적화 연구 ★(prefetch·속도 관련 후보)
- 2315944218 CI/CD 배포 플로우 신체계
- 2290221058 Platform Server CI — GitHub Actions
- 2328723662 GitHub Actions 빌드 러너 인프라 build-server
- 2274394290 ECR Docker 이미지 태그 컨벤션 통일
- 2274656402 클라이언트 배포 파이프라인 현황 분석
- 2222620673 다른 Vite 앱 확장 적용 결과

## D. 관측성 Datadog (→ _service-overview)
사용자 언급 "Datadog 본격화".
- 2356150511 Datadog 관측성 3일 실행 계획 — Gateway/Platform
- 2364440747 EB → Datadog 관측 표준(§7/§9) 정렬 — 종합 계획
- 2364310014 EB → Datadog §7/§9 Attribute 정렬 Before/After
- 2360410349 [Hub] 호주 업/다운로드 속도 30일 분석 — RUM raw
- 2369683499 Crown 데스크톱 "느림" 사례 분석 — Singapore/Sifary

## E. E2E/테스트 (→ 09, 발굴 완료 주제 보강)
- 2287206604 격리 개발 환경 (Local Dev Stack) ★
- 2286158153 E2E 격리 환경 (docker/e2e/)
- 2355003615 Playwright 시각 회귀 도입 가이드
- 2399174662 개발 데스크톱 인프라 — E2E Runner (192.168.10.192)
- 2369061053 Billing E2E 매트릭스 가이드
- 2372075613 / 2356150364 / 2297495553 QA transition / 회귀 보드 분석

## F. MFE/모듈 (→ 03, 08)
- 2282094594 VTK/iwtk → Three.js 전환 및 iframe 마이그레이션 결과 ★(08)
- 2282782951 ErrorBoundary 현황 분석 및 개선 제안
- 2411331634 Cloud Modules - Explorer, Viewer, Settings

## G. 결제/구독 Stripe (→ 04 Account 보강)
- 2426143016 Stripe 마이그레이션: 미국 법인 → 싱가폴 법인
- 2265415798 P010 - Stripe Connect Direct Charges 아키텍처
- 2276163909 Stripe JP 정기 결제 — Direct Debit
- 2177433602 Pricing 정책 변경 + Dealer Membership
- 2242576395 Open API 구독 시스템 통합 개요

## H. 측정/Analytics GA4 (→ _service-overview, 06 Landing 연관)
- 2250408398 GA4 이벤트 택소노미 v2 — 281개 이벤트
- 2383052830 GA4 전환 측정·Paid 광고 성과관리 설계 (Landing→Account→Subscription)
- 2268463356 Desktop Analytics — Metabase + dhub CLI
- 2356150563 Customer.io 전면 이전 작업 계획

## I. 백엔드 아키텍처 (→ 범위 축, 서비스개요)
- 2394423468 Milling Worker Server 아키텍처 변경 — Service Bus → Prepared Worker
- 2425946261 Crown 생성 오프로드 → milling-worker-server 통합 분석·설계
- 2229207294 Platform Server MongoDB 컬렉션 스키마
- 2300411905 WIP Lock 통합 전후 Poll API 아키텍처
- 2394882059 Preference / Setting / Preset 도메인 통합 (CRWN-3355)

## J. 도메인/멀티테넌트
- 2193588330 Dentbird 테넌트 및 도메인 시스템 현황
- 2391769104 [리서치] 서브도메인 통합 방안 — region path-prefix
- 2192900150 / 2193162324 iDD 전용 Tenant 생성

## K. AI 도구·팀 자동화 ★ (→ 99 misc — 리딩 경험, 차별화)
- 1947041920 Claude Code 핸즈온 — 프레젠테이션 대본 ★(팀 AI 도구 리딩 증거)
- 2389016603 dentbird-bot 구조와 할 수 있는 일
- 2275508256 / 1940488629 팀 봇 계정 (dentbird1_ai / D1-Owl) 운영 가이드
- 2218426594 CLAUDE.md 구조 개선 의사결정
- 2339733648 Batch CLI 기반 AI Agent 연동 기획

## L. 디자인 시스템 (→ 07)
- 1940946953 MUI 기반 디자인 시스템 마이그레이션 가이드
- 2162393237 Dentbird 보철물 디자인 시스템 — 가이드

## M. 스터디·엔지니어링 문화 (→ 99 misc)
- 1757544577 타입스크립트 챌린지 스터디 ★
- 2285895870 PR Complexity Leverage 분석 — 공유 모듈 변경 가중 영향도
- 2390982933 작업 문서의 Git 커밋 가이드라인
- 2282946692 FE/BE 버그 수정 가드레일

## N. 제품 스펙/SDS 정리 (대량 — UX/스펙 인벤토리 작업)
- 2398191635 / 2398322702 SDS 명세 공백 종합 감사 (Cloud / Batch)
- 2408841232 스펙·UX 정리 작업 현재 상황과 품질 문제
- 2368962563 Batch UX 폴리시 완성도 가시화 — Atomic Inventory
- Cloud Desktop/Batch chapter 문서 다수(3.x ~ 15) — 제품 스펙 reference map

---

## 발굴 우선순위 제안
1. **10 인증/보안** = A(파일 암복호화 AEAD) + B(JWT→세션). 본인 BE 기여·풍부.
2. **_service-overview 보강** = C(빌드 최적화)·D(Datadog)·I(앱/서버 통합). 팀 기술.
3. **99 misc** = K(AI 도구 리딩)·M(TS 스터디). 차별화·리딩 경험.
4. **08 iwtk→Three.js** = F(2282094594, 이미 일부 확보).

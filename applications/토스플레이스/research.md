# 토스플레이스 — JD·엔지니어링 컬처 리서치

> 작성: 2026-06-03 / 목적: 토스플레이스 FE 지원 이력서의 전략 근거 + 면접 준비
> ⚠️ JD 원본(toss.im job-detail)은 JS 렌더링이라 직접 추출 실패 → 원티드 미러+동일 직군 교차 복원. 우대사항 정확 문구 미확보.

## 1. JD 핵심 요구

**주요 업무 (책임 범위)**
- **POS 시스템 개발** — "웹 기술로 단일 소스코드로 Windows/macOS/Android/iOS 전 플랫폼 지원"
- 키오스크·테이블오더·픽업오더 등 오프라인 결제 서비스 개발·확장
- 파트너용 **B2B 플러그인·SDK 개발** (주문·결제 코어 로직 노출)
- 사장님 대시보드·파트너스·프랜차이즈 대시보드 구축

**필수 자격**
- React 등 SPA 프레임워크 능숙
- TypeScript / Flow 정적 타입 분석 경험
- **"복잡하고 어려운 요구사항을 단순한 문제로 추상화"하는 능력** (토스 FE 공통 핵심 문구)
- 주도적 문제 발견·해결

**도메인 키워드**: 오프라인 결제, POS, 키오스크, 결제 단말기, 하드웨어 연동, 매장 운영 솔루션, B2B SDK, 멀티플랫폼 단일 코드베이스

## 2. 회사가 푸는 문제 · 일하는 방식

**푸는 문제**: 오프라인 결제 시장(매장 운영). POS·키오스크·결제단말기라는 **하드웨어 위 소프트웨어**를 웹 기술 단일 소스코드로 전 OS에 제공. 하루 수십만 건 결제 안정 처리.

**핵심 아키텍처 진화 서사 (지원자 입장 가장 중요)**
초기 Electron 데스크톱 앱 → "버튼 하나 추가에도 전체 앱 빌드·배포" 문제 → **웹뷰 번들 아키텍처**로 재설계:
- 웹뷰 영역(UI+비즈니스 로직, 자주 변경) ↔ 네이티브 영역(하드웨어·보안·시스템) 분리
- 웹뷰 번들 CDN 독립 배포 → **배포 크기 100MB→5MB, 배포 시간 20분→1분**
- 주문/결제/시재를 독립 웹뷰 번들(MFE)로 쪼개 변경 격리
- 비즈니스 로직이 웹뷰에 있어 iOS/Android 1개월 만에 출시
- 네이티브 영역: E2E 암호화, **시리얼 포트/프린터 하드웨어 연동**, 자체 시리얼 프로토콜(핸드셰이킹·CRC·재시도), 무선은 DNS-SD 브로드캐스팅 + 웹소켓 자동 재연결

**일하는 방식**: **DRI(Directly Responsible Individual)** — FE가 담당 사일로 제품의 최종 의사결정권자. 사일로 4~8명 "작은 스타트업처럼". 챕터 단위 협업(위클리·Tech Talk·공용 라이브러리·코드 리뷰).

## 3. FE 기술 스택

- 코어: React, TypeScript, Flow
- 데스크톱/단말: **Electron + ffi(Foreign Function Interface)로 결제·하드웨어 연동**, 웹뷰 렌더
- 아키텍처: 모노레포(토스코어 단일 레포 100+ 서비스), 웹뷰 번들 MFE, 플러그인 구조
- 디자인 시스템: **TDS(Toss Design System)** — 모노레포 버전 관리로 FE/Android/iOS 일괄 배포
- 공용: `frontend-libraries` 자체 라이브러리 문화
- 관심사: 단일 코드베이스 멀티플랫폼, 무중단·고속 배포, 하드웨어 연동 안정성, **추상화**

## 4. 지원자 매칭 (강도 순)

1. **Electron 0→1 + 웹↔로컬 하드웨어/SW 연동** (≫ 압도적) — 토스 POS의 "Electron 네이티브 영역 하드웨어 연동"과 지원자의 **12종 CAM 연동 + Chrome LNA 대응 Custom Protocol**은 "웹뷰↔네이티브 브리지" 같은 종류의 문제.
2. **MFE + NX 모노레포 통합** — 토스 "웹뷰 번들=독립 배포 MFE", 모노레포 버전 관리와 직결.
3. **빌드·배포 최적화 감각** — 토스 자랑 "배포 100MB→5MB, 20분→1분" ↔ 지원자 빌드 20→6분.
4. **품질 자동화(E2E·Docker 격리·AI 변경감지)** — 하루 수십만 결제 = 회귀·안정성 생명.
5. **B2B 구독/결제 FE 전담 + 글로벌** — JD "B2B 플러그인·SDK, 사장님/프랜차이즈 대시보드"와 도메인 정합.

## 5. 이력서 강조 전략

"**웹 기술로 하드웨어 위 소프트웨어를 만들어 본 FE**" 단일 메시지에 화력 집중. 일반 React 대시보드 FE는 흔하므로, 결정적 차별점인 **Electron 0→1 + 웹↔로컬 디바이스 연동(LNA Custom Protocol, 12종 CAM)**을 첫 카드로. 토스의 "웹뷰↔네이티브 분리 + 시리얼 연동"과 같은 계열 난제로 프레이밍. 2축 NX 모노레포·MFE, 3축 결제 품질 자동화. DRI·추상화 컬처에 맞춰 각 카드를 문제→트레이드오프→재설계 구조로. 특히 Electron 빌드/배포 한계→재설계 서사는 토스의 동일 진화 경로와 정확히 겹침.

## 출처
- JD: https://www.wanted.co.kr/wd/207573 · https://toss.im/career/tossplace · https://toss.im/career/job-detail?job_id=4664498003
- 아키텍처: https://velog.io/@khy2106/토스-SLASH24-프론트엔드-트랙 · https://toss.im/tossfeed/article/tossplace-all-OS · https://toss.im/slash-21
- 문화: https://tossplace-frontend.oopy.io/ · https://blog.toss.im/article/toss-team-culture
- TDS: https://toss.tech/article/toss-design-system · https://toss.tech/article/rethinking-design-system

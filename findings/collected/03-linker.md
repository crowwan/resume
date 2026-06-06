# Dentbird Linker — 웹↔로컬 CAM 연동 Electron 앱 (LNA·Custom Protocol) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · 조회일 표시 생략
> 입력원: 1단계 git/PR + 2단계 Jira(assignee=currentUser()) + 본인작성 vault note. Confluence 사전 인덱스 없어 호출 안 함(추정 금지).
> ★ = 기존 findings(02-linker.md)에 없던 NEW. 기존 findings에 이미 있는 사실(20방안·WS⭐5/CP⭐4 트레이드오프·2/3/6초·$0/$10-50·Express 폐기·characterization test·draco3d 채택·crown-io 275줄 포팅·스파이크 4단계·8태그/4메이저)은 **중복 적재 제외**.

---

## 본인 기여 식별 요약
- **본인(jwkim) 확정**: DEN-4466·4503·4583·4600 및 "Export to SW"/Linker/CAM Jira 슬라이스(≥50건)는 `assignee = currentUser()`(jwkim) 조회 결과 = 본인 담당 `[jira]`. D1-1824(Datadog 연동)도 본인 담당 `[jira]`.
- **본인 작성 vault note**: `_onboarding/cam-export-protocol-and-lna.md`, `bugs/DEN-4466/investigation.md`, `bugs/DEN-4503/investigation.md`, `_onboarding/glossary.md`, `_onboarding/deploy-units.md` — investigation/learning 본인 작성(participants: 김진완 명시) `[vault]`.
- **팀/인접(경계)**: DEN-4583 버그 본체("서버 마스킹 binary leak")는 export 파이프라인 소관 — Linker는 검증 하네스 측. DEN-4194/4576/draco3d/WV-008는 export/viewer 카드 소관(draco3d 디코딩 공유 접점만 사실). **Linker 카드에 혼입 금지**.

---

## 1. 의사결정 흔적

- ★ **"데이터 채널 vs 감지 채널" 2채널 분리 프레이밍(본인)**. 웹→로컬앱 연동을 (1) 데이터 채널(파일 전달) = Custom Protocol + Export Session으로 **해결됨**, (2) 감지 채널(앱 설치/실행 여부 회신) = **미해결 잔여물**로 명시 구분. 이 분리가 DEN-4466/4503 근원 진단의 핵심 프레임 `[vault: _onboarding/cam-export-protocol-and-lna.md, 본인작성]`
- ★ **LNA가 "죽이는 것 / 안 죽이는 것" 매트릭스 정리(본인)**: web→`localhost` fetch/XHR = ❌차단 / `ws://localhost` = 현재 예외(단 "곧 LNA 적용, 1-2년" 위험) / **custom-protocol launch(navigation) = ✅무관**(LNA는 network request 대상이지 navigation 아님) / 앱→클라우드(네이티브) = ✅무관. → "감지를 살리려면 localhost가 아니라 **앱↔클라우드/앱↔웹 왕복**이어야 한다"는 설계 제약 도출 `[vault: cam-export-protocol-and-lna.md]`
- ★ **"HTTPS 로컬서버"가 LNA 우회책이 아닌 이유를 본인이 명시**: LNA는 프로토콜이 아니라 **네트워크 위치(public→private) 기반**이라 `https://localhost`로 바꿔도 차단됨. 20방안 중 "HTTPS 로컬서버" 탈락 근거의 1차 기록 `[vault: cam-export-protocol-and-lna.md]` (※ 기존 findings엔 20방안 존재 사실만 있고, **개별 안 탈락 근거는 없음 → NEW**)
- ★ **Custom Protocol 채택의 문서화된 단점 = "양방향 통신 불가(Health Check 제한)"** — 이 단점이 그대로 감지 채널 미해결의 출처가 됨. **장기 안정성을 위해 양방향성을 포기한 트레이드오프의 후과를 본인이 추적**(설계 결정 → 운영 버그 연결) `[code: COMPLETE_SOLUTION_ANALYSIS.md, vault 인용]` (※ 기존 findings는 "단방향" 사실만 언급, 그 후과 추적은 NEW)
- ★ **감지 채널 옵션 공간 6안을 본인이 정리·평가**:
  - ① 클라우드 왕복 ACK(session claim 폴링) — LNA-safe·결정론적, but ExportSession 스키마+엔드포인트 추가 필요 → **비선택**(백엔드 의존 회피)
  - ② custom-protocol echo(앱→웹 navigation 콜백 + token)
  - ③ **비-trapping UX 통일(batch식, 감지 포기·경로 항상 도달 가능)** — 사용자(본인) 선호 방향 후보
  - ④ localhost health 폴링 = ❌LNA 금지
  - ⑤ `ws://localhost` = ⚠️스톱갭만
  - ⑥ blur 휴리스틱 개선 = 근본 해결 불가
  `[vault: cam-export-protocol-and-lna.md]`
- ★ **사용자(본인) 발화 verbatim 보존**: *"로컬 서버랑의 통신은 구글의 LNA 정책때문에 안돼 그 이유때문에 링커도 로컬 서버가 아닌 커스텀 프로토콜 방식으로 변경한거야."* — career의 LNA→Custom Protocol 전환 사유를 본인 입으로 확정한 1차 증거 `[vault: bugs/DEN-4466/investigation.md, bugs/DEN-4503/investigation.md]`

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

- ★ **본인 담당 "Export to SW"/Linker Jira = 최소 50건**(이 JQL 슬라이스 한도), 기간 **2026-01-08 ~ 2026-06-04**. 월별 분포: 2026-01=22, 02=14, 03=6, 04=2, 05=5, 06=1. → "Linker 운영·안정화를 장기간 단독 책임"의 정량 근거(자체 추출, 추정 아님) `[jira: assignee=currentUser() AND (Export to SW/Linker/CAM)]`
- ★ **DEN-4583**(P1/버그, 생성 2026-05-18 → **종료 2026-05-27**): "[Export to SW] Export to SW 실행 시 An Unknown Error". 1단계의 RED-박제 회귀 가드(통합테스트 #9670, exportSession.integration.test.ts)가 닫은 티켓. Jira 메타로 **기간·우선순위(P1) 확정** `[jira: DEN-4583, git: dentbird-solutions 92d99f1]`
- ★ **DEN-4600**(P3/버그, 2026-06-04 **미해결**): "[Export to SW] Base & Die 포함 6개 이상 선택해 ALPHA AI 실행 시 An Unknown Error". 포트통신 SW(ALPHA AI) 운영 이슈가 현재진행형 → resume "현재까지 운영" 근거 `[jira: DEN-4600]`
- ★ **포트통신 health check 버그 이력(본인 담당)**: DEN-4364(Millbox·DGShape default port 연결 시도 안 됨), DEN-4359(connect 실패 시 port default 미초기화), DEN-4340/4345(port 입력값 미유지) — 포트통신 추상화의 운영 난점이 다수 티켓으로 실재 `[jira]`

## 3. 후속 영향

- ★ **DEN-4466 / DEN-4503 = "한 근원, 두 증상" 진단(본인)** — custom-protocol 전환의 **잔여 부채**가 운영 버그로 드러난 성장 서사:
  - **DEN-4466 (false-negative, Linker)**: Linker가 이미 실행 중이면 OS가 URL을 기존 프로세스로 라우팅 → 새 창 없음 → `window.blur` 미발생 → 10s timeout → "미설치 모달" 오표시. 과거 PR #6945(연결 체크 재시도)로 한 번 닫혔다 **reopen** — "휴리스틱 위에 보강을 쌓아도 근본 미해결"을 본인이 규명 `[jira: DEN-4466, vault: bugs/DEN-4466/investigation.md, PR: dentbird-solutions#6945]`
  - **DEN-4503 (false-positive, Batch)**: 미설치인데 로딩 중 DevTools 열어 포커스 빠지면 `window.blur` → `launched=true` 오판 → 설치 모달 억제. OS 알림·alt-tab·듀얼모니터도 동일 오염 `[jira: DEN-4503, vault]`
  - 근원 = `openProtocolWithFallback(protocolUrl, timeout)`의 `window.blur`/`visibilitychange` 휴리스틱. **copy 2개 존재**: `libs/cloud-core/src/utils/`(Batch) + `libs/embed-modules/.../ExportDialog/Utils/`(export). `useCamSWExportProtocol.tsx`가 회피용 10s timeout 사용(근본 해결 아님) `[code, vault]`
  - **본인 fix 방향(미정이나 결정 근거 명시)**: 백엔드 session-claim 폴링(클라우드 왕복)은 **검토 후 비선택**(앱 경계/백엔드 의존 확장 회피) → "감지를 load-bearing하게 두지 않는 batch식 비-trapping UX 통일" 선호 `[vault]`
  - ★ **회귀 가드 후보(본인 설계)**: "claim/확정 신호 없으면 window blur가 와도 `launched=false` 유지" 불변식 유닛 — 휴리스틱 회귀 방지 패턴 `[vault: bugs/DEN-4466/investigation.md]`
- ★ **현재 아키텍처(데이터 채널) 정확 경로**: 웹 `POST /v4/export-sessions`로 sessionId 발급 → custom-protocol URL에 sessionId 실어 발사 → Linker가 `GET /v4/export-sessions/{sessionId}/files`로 **클라우드에서 SAS URL self-fetch + count 차감**. URL 빌더 `buildSessionProtocolUrl`(env별 scheme), 호출부 `useCamSWExportProtocol.tsx`(`onExportToDentbirdLinkerCAM`/`onExportToDCAM`/`onExportToMillboxCompatible`) `[code, vault]` (※ 기존 findings는 개념 흐름 5단계만, 엔드포인트/심볼명 정확값은 NEW)
- ★ **서버 ExportSession 도메인에 claim/status/claimedAt 필드 없음**(`ExportSession.kt` = id/designCases/designCaseFiles 순수 레코드) → 클라우드 왕복 감지를 켜려면 서버 스키마+폴링 엔드포인트 신설 필요. "왜 쉬운 백엔드안을 안 골랐나"의 비용 근거 `[code: apps/platform/.../ExportSession.kt, vault]`
- ★ **Linker·Batch 둘 다 여전히 로컬 express 서버 보유**(Linker `:55000` `GET /api/health`·`POST /api/export` / Batch `healthRouter` 다수) — **단 LNA로 웹에서 직접 못 침**. "코드에 localhost health가 보여도 감지에 못 쓴다(legacy 잔재)"를 본인이 함정으로 박제 `[code: linker-desktop/src/server/server.ts, batch-native/src/api/app.ts, vault]` (※ 기존 findings는 "Express 더 이상 사용 안 함"으로만 — 실제론 코드 잔존+LNA로 호출 불가라는 **정밀 보강 = NEW**)

## 4. 운영 패턴

- ★ **배포 단위 사실**: `build-linker-desktop-windows.yml` = **Linker Windows-only 빌드**. linker-desktop은 dev/qa 미포함, **prod만 포함**. Batch는 macOS(Apple Notarization) + Windows(Sectigo USB) 양쪽. 출력 NAS = `\Imagoworks\Installation Archive\...` `[vault: _onboarding/deploy-units.md]`
- ★ **D1-1824(완료): Linker Desktop에 Datadog 연동** — Linker 운영 관측성 작업도 본인 담당. resume "운영·모니터링" 어필 근거 `[jira: D1-1824]`
- ★ **glossary 정본화(본인)**: Linker/Batch/Custom Protocol/LNA/Export Session/데이터·감지 채널 용어를 vault glossary에 정의 — 팀 온보딩 자산화 `[vault: _onboarding/glossary.md]`

## ★ 기존 findings/이력서에 없던 새 증거

1. **"데이터 채널 vs 감지 채널" 2채널 프레임(본인)** — custom-protocol이 데이터는 풀었으나 **양방향성을 포기한 트레이드오프가 감지 채널 부채로 귀결** → "결정→후과→재설계 검토" 성장 서사. (기존 findings에 없음)
2. **DEN-4466/4503 = 한 근원(blur 휴리스틱 오염) 두 증상** + 백엔드안 검토 후 비선택 결정 근거 + 회귀 가드(claim 없으면 launched=false 불변식) 설계.
3. **LNA "죽이는 것/안 죽이는 것" 매트릭스 + HTTPS 로컬서버 탈락 근거**(네트워크 위치 기반) — 20방안 분석 속 본인 사고 과정의 1차 기록.
4. **Jira 정량**: "Export to SW"/Linker 본인 담당 ≥50건(2026-01~06), DEN-4583(P1, 5/18→5/27 종료), DEN-4600(현재 미해결), D1-1824(Datadog 연동), 포트통신 health 버그군(DEN-4364/4359/4340/4345).
5. **운영 경로 정확값**: `/v4/export-sessions` 발급 → sessionId 발사 → Linker self-fetch + count 차감 / `ExportSession.kt`에 claim 필드 부재 / Linker(`:55000`)·Batch 로컬서버는 존재하나 LNA로 웹 직접 호출 불가(legacy 잔재).
6. **배포 단위**: Linker Windows-only·prod-only 빌드 / Batch macOS+Windows 양쪽 서명 체계 차이.

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- **CAM SW 수**: 8(linker-app) → 12(linker-desktop). career의 "16"은 여전히 코드 미확인 — 현재 정확 수치 확인 필요 `[측정필요]`
- **DEN-4466/4503 fix 최종 결론**: 6안 중 "③ 비-trapping UX 통일"이 사용자 선호 후보로만 기록됨. **실제 머지/적용 여부, 적용했다면 PR 번호와 결과**가 미확정 `[기억/측정필요]`
- **회귀 가드 유닛("claim 없으면 launched=false")**: 설계 후보로만 vault에 존재. **실제 테스트 작성·머지 여부** 확인 필요 (미작성이면 이력서에 성과로 쓰지 말 것) `[측정필요]`
- **D1-1824 Datadog 연동 임팩트**: "연동했다" 외에 무엇을 관측 가능하게 만들었는지/어떤 운영 효과가 있었는지 정성 근거 필요 `[기억]`
- **"Export to SW" 50건의 jwkim 단독 처리 비율**: 슬라이스가 assignee 기준이나, 그중 본인이 직접 코드 수정·해결한 비율 vs 트리아지/이관은 모호 `[기여확인]`
- **DEN-4466의 과거 PR #6945**: "연결 체크 재시도"가 본인 작업인지 타인 작업인지 author 미확인 `[기여확인]`

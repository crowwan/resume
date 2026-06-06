# Imago Cloud Design System 기여 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리
> 기존 findings/07-design-system.md 대비 **새로운/보강된 사실만** 적재 (중복 제외)
> 발굴 지형: DS 패키지가 **2개로 분기**되어 있고 본인이 양쪽에 기여 (기존 07은 1개로만 인식)

## 본인 기여 식별 요약

- **본인(jwkim, author/assignee) 확정**:
  - Azure `@imago-cloud/design-system`: DatePicker onClose/placeholder/onClick 확장 3건 + release 태깅 3건(2.0.2/3.0.0-13/3.0.0-15) + react19 마이그레이션 wip 1건(미머지 브랜치). *(이 중 onClose/placeholder/onClick·릴리스는 07에 이미 있음 → 본 파일에서는 재적재 X)* [git: imago-design-system]
  - 모노레포 `@imagoworks-inc/design-system`(v3.0.0-28): **★ DEN-548 DatePicker 포커스 인디케이터 수정 + 소비처 3앱 배선 + E2E 회귀**(#6511). 완전 신규. [git: dentbird-solutions@d0c5e48 (#6511)]
  - **★ D1-2838 imago-theme 롤백 전담**(assignee 본인): DS 내부 Snackbar/DialogueTitle remixicon 원복(PR #31251) + cloud-desktop/mobile 토큰·Typography·Ricon 일괄 복원. 완전 신규. [jira: D1-2838, git: dentbird-solutions@27066a7]
- **팀/타인([팀] 태그)**:
  - MUI 마이그레이션 전략 9문서 = **Hyeoncheol Kim 작성** (본인 아님). 본인은 그 전략을 소비하는 입장. [confluence: 1940946953]
  - imago-theme 패키지 **도입(마이그레이션) 자체**는 본인 아님 — 본인은 그 **롤백·복원**을 맡음. ★ "디자인 토큰 시스템을 설계했다"는 본인 기여 **아님**. "성급한 마이그레이션을 안정성 위해 롤백 주도"가 정확. [jira: D1-2838]
  - v3 DatePicker **재작성 주체**(useSplitFieldProps/usePickerContext 기반)는 별도 — 본인은 그 위에 calendarOpen/placeholder/onClick 확장만. [code: DatePicker.tsx]

## 1. 의사결정 흔적

- **★ NEW: DEN-548 포커스 인디케이터 — 근본 원인형 수정.** DatePicker Custom 모드에서 캘린더가 외부 open 상태로 열렸을 때 MUI `Mui-focused` 클래스가 안 붙어 파란 밑줄(포커스 인디케이터)이 표시되지 않던 버그. 해결: `DatePickerInput`에 `calendarOpen?: boolean` prop 추가 → `const isOpen = calendarOpen ?? pickerContext.open` 폴백 → `className`에 `${isOpen ? ' Mui-focused' : ''}` 합성. MUI 내부 포커스 상태(pickerContext.open)와 외부 제어 상태가 어긋나는 지점을 정확히 짚어 **외부 open을 우선하되 내부 상태로 폴백**하는 패턴으로 고침. [git: dentbird-solutions@d0c5e48 (#6511)] [code: libs/imago-cloud-design-system/src/Components/Inputs/DatePicker/DatePicker.tsx]
- **★ NEW: 동일 옵셔널-폴백 패턴 4건째.** DEN-548의 `calendarOpen ?? pickerContext.open` 폴백은 07의 placeholder(`?? parsedFormat`)·onClose(콜백 체이닝)·onClick(체이닝)과 **같은 꼴**. "MUI 기본 동작 보존 + 옵셔널 prop로 외부 제어 추가, 없으면 내부값 폴백" 패턴이 4번째로 반복 → 의식적 하위호환 확장 감각이 더 강하게 입증됨. [git: a577470, baa1797, b434772, d0c5e48]
- **★ NEW: D1-2838 imago-theme 롤백 결정 (본인 assignee).** 신규 `@/imago-theme` 패키지 도입 후 디자인 토큰이 **확정되지 않은 상태에서 마이그레이션**해 cloud-desktop/cloud-mobile에서 디자인 깨짐 다발. 결정: "토큰 확정 전까지 구 DS(`@/imago-cloud-design-system`)로 롤백 → 안정성 확보 후 토큰 확정 시 재마이그레이션". 성급한 마이그레이션을 되돌리는 판단 — 이력서 CLAUDE.md의 "학습·성장 서사"에 부합(발명 아님, 티켓에 명시). [jira: D1-2838] [git: dentbird-solutions@27066a7 (PR #31251)]
- **★ NEW (팀 맥락, 출처 근거): MUI 마이그레이션 전략 문서 = Hyeoncheol Kim 작성.** `@imago-cloud/design-system` → MUI 네이티브 테마 시스템 전환 가이드(9개 하위문서). 핵심 원칙 "MUI를 커스텀하는 게 아니라 MUI가 제공하는 커스텀 시스템을 사용한다". 07에서 본 "MUI 커스텀 시스템 원칙"의 출처가 이 **팀 전략 문서**임이 확정. 본인 DatePicker 작업은 이 팀 전략 안의 슬라이스. [confluence: 1940946953, 작성자 Hyeoncheol Kim]

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

- **★ NEW: DEN-548 수정 규모** — DS lib `DatePicker.tsx` **+8/-3** 라인 + 소비처 3앱(cloud-desktop/account-client/cloud-mobile) DateRangePicker 각 `calendarOpen` 전달 **+2씩** + E2E 회귀 테스트 신규 `DEN-548.test.ts` **121라인** + WorkspaceLogsPage PO **+25**. 커밋 전체 8파일 **+308/-9** (단 이 중 152라인은 `.claude/skills/work/SKILL.md` 문서 — 코드 변경분과 분리해 인용). [git: d0c5e48 --stat]
- **★ NEW: DEN-548은 소비처까지 일관 적용** — DS 1개 컴포넌트 수정에 그치지 않고 **실제 3개 소비 앱의 DateRangePicker에 `calendarOpen` 배선**까지 본인이 직접. 07의 [측정필요] "확장 props의 실제 소비처"가 여기서 부분 해소(My Designs 날짜 필터, account WorkspaceLogs). [git: d0c5e48 -- apps/*]
- **★ NEW: D1-2838 롤백 규모 (티켓 명시 계획값, 실측 아님 — `~`/"약" 표기 유지)** — 대상 앱 cloud-desktop **~79개 파일**, cloud-mobile **~35개 파일**. Typography variant **47개 커스텀 variant** 구 DS 방식 복원. 안전 파일 **~109개** 스크립트 자동 복원 + 비즈니스 로직 겹침 **5개** 수동 머지. 예상 소요 **~3시간**. ★ 이 수치들은 티켓 description의 **계획값**이므로 인용 시 "티켓 기준 약 ~" 식으로만. [jira: D1-2838]
- **★ NEW: D1-2838 PR #31251 실제 머지분(DS 내부)** — DS `Snackbar.tsx` **+2/-2**, `DialogueTitle.tsx` **+2/-2** = 2파일 4라인. close 버튼 아이콘 `@remixicon/react`(`RiCloseFill`) → DS 자체 `Ricon` 원복(색상 미적용 버그 수정). imago-theme 마이그레이션 커밋 `7edc9c7a99`에서 바뀐 걸 되돌림. [git: dentbird-solutions@27066a7 --stat]

## 3. 후속 영향

- **★ NEW: DEN-548은 회귀 테스트로 가드됨** — `apps/e2e/cloud/tests/bugs/DEN-548.test.ts` 신규. SDS(`docs/sds/cloud/03-cloud.md §9`) 기대 동작을 근거로 작성: "Custom 클릭→Start date 포커스(파란 밑줄), Start 선택→End date로 포커스 이동". 검증 = `MuiInputBase-root`에 `Mui-focused` 클래스 존재 단언. **버그 수정 + 회귀 가드를 한 커밋에**(Bug Fix TDD). My Designs + Workspace Logs 양쪽 커버. [git: d0c5e48, code: DEN-548.test.ts] [code: docs/sds/cloud/03-cloud.md §9 — SDS 근거]
- **★ NEW: DS 패키지 분기 사정 확정** — Azure `@imago-cloud/design-system`(v2.0.2/v3.0.0-xx)과 모노레포 `@imagoworks-inc/design-system`(v3.0.0-28)은 **서로 다른 패키지/레포**. 본인 DatePicker 작업이 양쪽에 걸쳐 있음. 패키지가 둘로 나뉜 건 분기/마이그레이션 와중의 상태 — 발명 말고 사실대로. [git: imago-design-system package.json / dentbird-solutions libs/imago-cloud-design-system/package.json]

## 4. 운영 패턴

- **★ 공통 운영 패턴(전 4건 DatePicker)**: "MUI 기본 동작 보존 + 옵셔널 prop로 외부 제어 노출 + 외부값 우선/내부값 폴백 + 하위호환". onClose(콜백 체이닝)·onClick(체이닝)·placeholder(`?? parsedFormat`)·calendarOpen(`?? pickerContext.open`) 모두 동일 꼴. 카드의 "하위호환 확장 감각·MUI 래퍼 설계 일관성"으로 어필 가능. **단 규모는 단일 컴포넌트 수준**(과대포장 금지). [git: a577470/baa1797/b434772/d0c5e48]
- **★ 롤백·복원 운영 패턴**: D1-2838에서 안전 파일은 **스크립트로 자동 복원(~109개)** + 비즈니스 로직 충돌 영역만 **수동 머지(5개)**로 분리 처리. 대량 변경을 자동/수동으로 나눠 안전하게 되돌리는 운영 감각. [jira: D1-2838]
- **vault 기여 없음**: vault에 DS 결정/측정/재설계 노트 없음. 언급은 맥락 수준만 — repo-map.md(레포 구성요소 나열), conventions.md(`@imagoworks-inc/design-system` 동적 import 예외), glossary.md(브랜드 용어). 이 영역 [측정필요]는 vault로 해소 불가. [vault: _onboarding/repo-map.md:105, _onboarding/conventions.md:128, glossary.md:60]

## ★ 기존 findings/이력서에 없던 새 증거

1. **DS 패키지가 2개** — `@imago-cloud/design-system`(Azure) / `@imagoworks-inc/design-system`(v3 모노레포). 기존 07은 1개로만 인식. [git: 양 레포 package.json]
2. **DEN-548 DatePicker 포커스 인디케이터 수정**(#6511) 완전 신규 — DS lib(+8/-3) + 소비처 3앱 배선 + E2E 회귀(121라인). 근본원인형, 동일 옵셔널-폴백 패턴 **4건째**. [git: d0c5e48]
3. **D1-2838 imago-theme 롤백** 완전 신규 — 본인 assignee, "성급한 마이그레이션 → 안정성 위해 롤백 → 토큰 확정 후 재마이그 계획"의 판단/성장 서사. DS 내부 실제 머지(Snackbar/DialogueTitle +2/-2씩). [jira: D1-2838, git: 27066a7]
4. **MUI 전략 문서 작성자 = Hyeoncheol Kim([팀])** — 본인 DatePicker 작업이 이 팀 전략 안의 슬라이스임 확정. 07의 "MUI 커스텀 시스템 원칙" 출처가 이 문서. [confluence: 1940946953]
5. **07의 [측정필요] "확장 props 소비처" 부분 해소** — DEN-548 건은 My Designs 날짜필터·account WorkspaceLogs로 소비처가 git상 확정. [git: d0c5e48 -- apps/*]

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

- 디자인 시스템/MUI 마이그레이션으로 Design QA 에러가 실제 줄었는지 정성·정량 — 전략 문서의 "목표"일 뿐, 본인 작업의 결과 지표 아님(발명 금지). [측정필요]
- D1-2838 롤백 수치(~79/~35 파일, 47 variant, ~109 자동+5 수동, ~3h)는 티켓 **계획값**(`~` 표기). 실제 머지된 파일 수 정밀치는 PR #31228/diff 전체 확인 필요(현 단계 미확인). 인용 시 "약/티켓 기준" 유지. [측정필요]
- react19 마이그레이션은 여전히 **미머지 탐색 wip** — "도입" 금지, "호환 탐색 wip"만. [git: 1cfdf05]
- DEN-548 수정으로 사용자/QA가 실제 체감한 개선(포커스 인디케이터 미표시 클레임 빈도 등) 정성 근거. [기억]
- D1-2838 롤백이 그 후 "토큰 확정 → 재마이그레이션"으로 실제 이어졌는지(지속성/후속 서사). [기억/측정필요]

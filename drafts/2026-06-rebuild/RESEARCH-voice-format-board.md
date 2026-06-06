# 보이스·포맷 레퍼런스 보드 (2026-06 rebuild)

> 6방향 리서치(en-narrative / dev-portfolio / outcome-xyz / kr-unconventional / decision-log / minimal-confident) 합성.
> 목적: 한국 IT 이력서 컨벤션 — `~했습니다` + **`판단:` 반복 tic** + 압축불릿(괄호수치 폭격) — 을 **벗어난**, 담백하지만 차별화되고 신뢰가는 보이스·포맷을 진완 자산에 이식하기 위한 카탈로그.
>
> **사실 가드레일(발명 NO).** 모든 진완 적용 예문은 *구조 제안*일 뿐. 수치·폐기/재설계 사실은 진완이 실제 확인한 값에만 적용한다.
> - 웹 빌드 수치 금지 / "6분" 금지 / 인스톨러 854→78MB는 '설정 누락 수정'이라 정량 자산으로 쓰지 않음
> - CAM = **12종** (16 아님)
> - blur 휴리스틱은 "개선 검토 중"(해결 성과 아님)
> - PNA ❌ → **LNA**(Local Network Access)
> - LNA 우회 = **20여 방안 탐색 → Custom Protocol 채택**(장기 안정성 기준). 문서는 'AI 활용 탐색 + 본인 의사결정'이지 '4,000줄 작성'이 아님

---

## 0. 우리가 벗어나려는 컨벤션 (대조군)

한국 IT 이력서의 전형 — 의도적 반면교사로 박제한다:

```
- 구조를 설계합니다 → 분산 앱 NX 모노레포 통합 · 공통 모듈 MFE 분리 · ...
- 판단으로 결정합니다 → LNA 우회 20여 방안을 탐색해 ...
- 효율을 높입니다 → 빌드 6분 → N분 (괄호수치)
```

이 컨벤션의 3대 tic:
1. **압축불릿 + 괄호수치 폭격** — 맥락 없이 결과 숫자만 가둔다. 면접에서 한 줄을 풀어 말하기 어렵다.
2. **`판단:` `구조:` 라벨 반복** — 판단을 별도 라벨로 박아야만 판단처럼 보인다는 착각. 산문이면 흐름 자체가 판단이 된다.
3. **동사+수치 머리 + `~했습니다` 끝맺음의 단조 리듬** — 모든 문장이 같은 모양이라 강약이 사라진다.

> 이 셋을 버리는 게 이 보드의 전부. 아래 아키타입은 전부 '셋 중 무엇을, 어떻게 대체하는가'로 읽으면 된다.

---

## 1. 보이스 아키타입 (7개)

각 항목: **이름표 / 한 줄 정의 / 대표 예시(출처) / verbatim 발췌 / 신선·신뢰 근거 / 진완 적용 시 느낌 / vs 한국 컨벤션**.

---

### V1. 물려받은 부채 → 판단 → 시차 결과 → 지속성 (서사 아크)

- **정의**: 한 직무/한 결정을 *불릿 0개, 4문장 산문*으로. '맥락 → 내가 물려받은 상태 → 내가 내린 판단 → 시간차를 둔 결과 → 지금의 지속성' 인과 아크로 흐른다.
- **대표 예시**: ResumeProfessionalWriters — Narrative Resume 샘플 (Senior Program Manager). https://www.resumeprofessionalwriters.com/how-to-write-a-narrative-resume/
- **verbatim 발췌**:
  > "Led the automation rollout across three regional distribution centers — a $4.2M program touching engineering, operations, and a 12-person cross-functional team. **The project had slipped once before I inherited it**, so my first 90 days were spent rebuilding the delivery plan and renegotiating scope with operations leadership. We shipped the first site on the revised timeline and **used what we learned to cut processing time by 34% the following year**. The program is now **the template the company uses** for new-site launches."
- **신선·신뢰**: 형용사 자랑('탁월한/혁신적') 0개. '내가 물려받기 전에 한 번 엎어졌다(slipped once before I inherited it)'는 불리한 사실을 먼저 인정해 신뢰가 오른다. 결과를 '다음 해(the following year)'로 시차를 둬 인과를 과대주장하지 않는다.
- **진완 적용 느낌**: 진완 4축(범위·구조·판단·효율)과 거의 1:1. 특히 차별점인 '자기 결정의 후과 추적'을 이 아크가 그대로 담는다. CLAUDE.md의 '학습·성장 서사로 정직하게' 룰과 완전 합치. 단 진완은 1인 전담 범위라 '$4.2M·12명' 규모 과시는 빼고 scope를 '책임의 폭'으로 재정의.
- **vs 한국 컨벤션**: 압축불릿·괄호수치·`판단:` 라벨을 한 방에 다 버린다. 가장 멀리 가는 형식.

---

### V2. 교차점 좌표 선언 (intersection stance)

- **정의**: 요약 첫 줄을 직함/라벨이 아니라 *'나는 X와 Y의 교차점에서 가장 잘한다'*는 좌표 선언으로. 라벨이 아니라 입장(stance)을 판다.
- **대표 예시**: Brittany Chiang 개인 사이트. https://brittanychiang.com/
- **verbatim 발췌**:
  > "I'm a frontend engineer with an expertise in building accessible, pixel-perfect user interfaces. **I do my best work at the intersection of design and engineering**, where great UX meets clean, scalable code. Currently, I'm on the component library team at Klaviyo... I lead engineering efforts across components, tooling, and patterns."
- **신선·신뢰**: 'pixel-perfect / accessible'처럼 검증 가능한 구체 기준만 형용. '교차점'은 허세가 아니라 자기 한계를 정직하게 좁히는 선언(=모든 걸 다 잘한다고 안 함).
- **진완 적용 느낌**: 포지셔닝 1안을 한 문장에 압축. 예) "웹과 로컬 디바이스가 만나는 경계에서 가장 잘합니다 — 브라우저가 못 닿는 곳(Electron·Custom Protocol·LNA 대응)을 메우면서, 렌더 품질을 측정 가능하게 유지하는 일." resume.md 최상단 또는 개인 사이트 헤더용.
- **vs 한국 컨벤션**: '○년차 ○를 다루는 개발자입니다' 라벨 나열을 좌표 선언으로 교체.

---

### V3. `I work on X:` 콜론 정의문 (깔때기)

- **정의**: 첫 줄을 *'나는 X를 하는 사람이다:'* 콜론 정의문으로 열고, 콜론 뒤에 X가 무엇인지 평이하게 정의. 이어 미션 → 관심영역 → 직함·이력으로 좁혀지는 깔때기.
- **대표 예시**: Geoffrey Litt — geoffreylitt.com (Notion, ex-Ink & Switch). https://www.geoffreylitt.com/
- **verbatim 발췌**:
  > "I work on malleable software: computing environments where anyone can adapt their software to meet their needs with minimal friction."
  > (마지막 한 줄 역량 요약) "designing and prototyping environments for thinking."
- **신선·신뢰**: 정의가 구체적이고 검증 가능('minimal friction')해 buzzword가 들어설 자리가 없다. 이력(Notion / Ink & Switch / MIT PhD)은 형용사 없이 사실로만 압축.
- **진완 적용 느낌**: 요약 첫 줄을 한국어 정의문으로 — "저는 웹앱이 로컬 하드웨어·OS와 안전하게 대화하게 만드는 일을 합니다: Electron 브리지, Custom Protocol, Chrome LNA 정책 대응까지." 콜론 뒤에 '그게 뭔지'를 평이하게 정의하면 압축불릿보다 읽기 쉽고 차별화.
- **vs 한국 컨벤션**: 'FE | React | 3yrs' 라벨 나열을 '문제영역 정의'로 교체. `판단:` 헤더 없이 포지셔닝이 첫 줄에 박힌다.

---

### V4. 한계 먼저 인정 (정직한 자기-디플레이션)

- **정의**: 약점/자기폐기를 *먼저* 말해 뒤따르는 강점 주장의 신뢰도를 끌어올린다. 직함 자체를 '덧없다'며 라벨 게임을 거부하고 '경계(intersection)'로 자기를 설명.
- **대표 예시**: Maggie Appleton — maggieappleton.com/about (design engineer, ex-GitHub Next). https://maggieappleton.com/about
- **verbatim 발췌**:
  > "Designer, anthropologist, and **mediocre developer**."
  > "I sit at the intersection of design, anthropology, and web development."
  > "Titles and disciplines are fickle and fleeting."
  > "I made **a lot of hideous stuff**, but figured out what I liked along the way."
- **신선·신뢰**: 강점만 부풀리는 한국 이력서와 정반대로 한계('mediocre')를 먼저 말한다 — 그 자체가 강한 자신감 신호. CLAUDE.md '발명 NO / 진짜 이유 없으면 정직한 성장서사로'와 같은 철학.
- **진완 적용 느낌**: 진완 최대 차별점('자기 결정의 후과 — 부채·자기폐기 추적')을 보이스로 구현하는 직접 모델. 예) "제가 도입한 분류 구조를 표준에 맞춰 제가 다시 걷어낸 적도 있습니다. 그게 제 방식입니다 — 결정의 수명을 끝까지 추적합니다." 미들→시니어 어필에 특히 강함. **단, 실제 폐기/재설계 사실이 있는 카드에만**(자기 분류 폐기 등).
- **vs 한국 컨벤션**: '성과만 진열대'를 깬다. 겸손의 신호 비용으로 신뢰를 산다.

---

### V5. 인과 체인 한 문장 (XYZ 변형 — 수치 없이도 성립)

- **정의**: *'[결과 동사] by [방법 Z], allowing/which [2차 효과]'* — 한 문장에 무엇이 → 어떻게 → 그래서 무슨 일이. 수치는 괄호 꼬리표가 아니라 'from X to Y' 자연어 대비로 문장 안에 녹임.
- **대표 예시**: Jo Sprague — localjo/resume (GitHub README). https://raw.githubusercontent.com/localjo/resume/master/README.md
- **verbatim 발췌**:
  > "Saved our team thousands of dollars in engineers' time every week **by replacing a manual QA process that took weeks with automated browser tests that ran in minutes**."
  > "Reduced deployment time **from over an hour to <5 minutes, allowing us to rapidly release** new features and bug fixes."
- **신선·신뢰**: 정밀 %가 없어도 'from weeks to minutes' before/after 대비로 검증 가능하고 과장이 아니다. 추정 %를 만들지 않는다 — 진완 Hard No('수치 추정 금지')와 정합.
- **진완 적용 느낌**: 픽셀 차분 회귀 검출이 이 틀에 딱. "3D 렌더 회귀를 매 배포 자동 검출해, **사람이 눈으로 비교하던 검수를 픽셀 차분 파이프라인으로 대체**했고, 회귀가 운영 전에 잡히게 됐습니다." 정확한 %가 없어도 '눈 검수 → 자동 검출' 대비로 임팩트가 선다.
- **vs 한국 컨벤션**: '괄호 수치 불릿' → '마침표 있는 완결 문장 + 인과 체인'. `판단:` tic 없이 인과를 문장에 녹인다.

---

### V6. before/after 구조 대비 (정성 임팩트 — 누구의 고통이 줄었나)

- **정의**: 수치가 0이어도 *'from [낡은 형태] into [새 구조], reducing/freeing [누구의 고통]'*으로 변화의 *형태*를 묘사하고, 결과를 '누가 무엇이 덜 아파졌는지'로 정성 서술.
- **대표 예시**: notchresume — 수치 없는 임팩트 불릿 가이드. https://notchresume.com/resources/resume-bullet-points.html
- **verbatim 발췌**:
  > Weak: "Helped with onboarding new employees."
  > Strong: "Redesigned the new-hire onboarding process **from a loosely organized checklist into a structured two-week program**, reducing the number of repeated questions from new hires and freeing up manager time during the first month."
  > (핵심 전환) "responsible for X" → "**did X, which resulted in Y**".
- **신선·신뢰**: 측정 못 할 'effectiveness/efficiency' 빈말을 안 쓰고 'repeated questions 감소', 'manager time 확보'처럼 관찰 가능한 행동 변화만 말해 과장이 아니다. 진완 최대 통증(제품 수치 약함)을 정확히 해결.
- **진완 적용 느낌**: 관측 표준화(ErrorBoundary·Datadog)에 직격. "에러가 사일런트하게 묻히던 상태에서 → 경계로 격리되고 Datadog에 표면화되는 구조로 바꿔, QA·CS가 재현 못 하던 이슈를 추적 가능하게 했습니다." %가 없어도 '누가(QA/CS) 무엇이 가능해졌나'로 임팩트가 선다.
- **vs 한국 컨벤션**: '효율 개선/품질 향상' 측정불가 빈말을 '누구의 고통이 줄었나'로 교체.

---

### V7. 담백한 분석 톤 + 의도적 과소표현 (decision-log)

- **정의**: 자기 일을 '대단한 것'으로 포장하지 않고 오히려 깎되('anyone could have done'), 임팩트는 정확히 적시. 트레이드오프/근거를 자랑이 아니라 *'왜 그게 효과적이었나'의 분석*으로 서술. '누가 효과를 봤나(who was it for)'를 문장에 명시.
- **대표 예시**: Dan Luu — danluu.com/programmer-moneyball/ · Julia Evans — jvns.ca/blog/brag-documents/
- **verbatim 발췌**:
  > (Luu) "I started typing up meeting notes for all of our local meetings... That's something **anyone could have done**, and it was **a huge productivity improvement**."
  > (Evans) "The impact of the project — **who was it for?** Are there numbers you can attach to it?" / show your work "**exactly as good as it is**".
- **신선·신뢰**: 자기 기여를 의도적으로 과소표현하니 남은 임팩트 주장이 오히려 믿긴다. Evans는 '실제만큼만, 부풀리지 말 것'을 명시 룰로 박아 과장을 구조적으로 차단.
- **진완 적용 느낌**: '자기 결정의 후과 추적' 강점에 가장 가까운 톤. "당시엔 빠른 도입이 우선이라 X를 택했고, 운영하며 한계를 겪어 Y로 재설계했습니다"를 자랑이 아니라 분석으로. **단 미들→시니어 어필이므로 Luu식 과소표현은 한 스푼만**, 임팩트는 분명히 적시.
- **vs 한국 컨벤션**: '동사+수치 머리'를 버리고 '왜 효과적이었나'의 분석 흐름으로. 판단을 라벨로 안 박고 흐름 자체가 판단이 되게.

---

## 2. 포맷·레이아웃 아키타입 (5개)

구조/섹션 구성 차원. V(보이스)와 직교 — 어떤 보이스든 아래 포맷에 담을 수 있다.

---

### F1. 직무당 2~3 산문 단락 (Context / Decisions / Outcome)

- **구조**: 직무당 불릿 6개 대신 *2~3개 짧은 산문 단락*. 각 단락 = 상황(scope/문제) → 내가 내린 결정 → 근거·트레이드오프 → 후과(누가 효과 봤나).
- **출처**: Narrative Resume(resumeprofessionalwriters), Teal 'narrative resume' 학파.
- **진완 핏**: 현 CLAUDE.md 4축(WHY/HOW/RESULT/지속성)과 자연 정합. 'Context→Decisions→Outcome' 3축에 **'지속성·한계' 한 문장만 더하면** 진완 차별점이 완성. 복잡한 기술 작업일수록 불릿으로 쪼개면 의미가 사라지는 진완 카드(Linker LNA 전환)에 최적.
- **리스크**: ATS 파싱에 약함 → long-form(개인사이트/모바일/리크루터 직접발송)과 ATS 제출용은 분리 운영.

---

### F2. 한 줄 정의문 헤더 + 깔때기 (Mission → Focus → Facts)

- **구조**: 맨 위 한 줄 정의문(V2/V3) → 핵심 관심 2축 → 소속·기간 사실 압축 → 마지막 한 줄 역량. 불릿/수치/괄호 없이 좁혀지는 깔때기.
- **출처**: Geoffrey Litt, Brittany Chiang, Rauno Freiberg 헤더.
- **진완 핏**: resume.md / 개인 사이트 / 노션 이력서의 **최상단 요약 블록** 전용. 라벨 나열형 한국 요약과 즉시 차별화. 진완은 본문은 경어체 산문(F1), 헤더만 이 깔때기로.
- **리스크**: 본문까지 이 톤이면 B2B 결제·품질자동화 자산이 죽음 → 헤더 한정.

---

### F3. 영문 한 단어 섹션 헤더 + 작동방식 정체성

- **구조**: 섹션명을 '경력사항/자기소개' 표 헤더 대신 마침표 붙은 영문 한 단어(`Bridge.` `Quality.` `Scope.` `Introduce.`). 본문은 '직함 나열'이 아니라 '한 가지 작동방식'으로 비선형 커리어를 묶음.
- **출처**: Wonny(wonny.oopy.io) — 'Introduce.' / 데이터로 일하는 개발자 작동방식 정체성. 정종윤 — 캐치프레이즈 거부 + 검증가능 사실.
- **진완 핏**: 진완은 미들급인데 역할이 넓다(브리지/플랫폼/품질/구독결제) — 이걸 약점(잡다함) 아니라 '한 작동방식'으로 묶음: '경계를 직접 메우는 FE'. 섹션 헤더 `Bridge. / Quality. / Scope.`로 4축을 컨벤션 탈출.
- **리스크**: 형식만 빌리고 캐치프레이즈/별칭 라벨은 피할 것(정종윤 경고 — 라벨이 실력과 불일치하면 신뢰 훼손).

---

### F4. 가치 litany 블록 (동사 리듬 대구)

- **구조**: `판단:` `구조:` 라벨 반복 대신, 가치를 *짧은 단문 대구의 리듬*으로 압축한 한 블록. ('범위를 좁힌다. 구조를 남긴다. 부채를 추적한다. 끝까지 책임진다.')
- **출처**: Rauno Freiberg — "Make it fast. Make it beautiful. Make it consistent. ... Make it." (rauno.me)
- **진완 핏**: 4축(범위·구조·판단·효율)을 슬로건이 아니라 리듬으로. resume.md의 `판단:`/`구조:` 라벨 반복 tic을 정확히 대체. **단 경어체 룰(6장) 충돌 회피 위해 '~다' 단문 리듬은 이 litany 블록에만 제한**, 본문 서술은 '~했습니다' 유지.
- **리스크**: 남발하면 슬로건 인플레 → 헤더/요약에 한 블록만.

---

### F5. 듀얼 트랙 (Long-form 산문본 ↔ ATS 압축본)

- **구조**: 같은 사실을 두 버전으로 운영. (a) 개인사이트/모바일Claude/리크루터 직접발송용 = F1+산문 보이스, (b) ATS 제출용 = 컨벤션 불릿(괄호수치 포함) 별도 유지.
- **출처**: StoryCV의 반론('산문은 ATS 파싱에 약하다')을 실무 경고로 채택.
- **진완 핏**: 진완은 라인/오늘의집/채널톡/토스플레이스에 ATS 제출 + 직접 어필 둘 다 필요. '컨벤션 탈출'은 직접발송본에서, ATS본은 안전하게 불릿 유지.
- **리스크**: 두 본 사이 사실 불일치 방치(기간/수치/명칭) — CLAUDE.md Hard No. 한 소스에서 파생시켜 동기화.

---

## 3. 진완 추천 TOP 3 조합

각 조합 = (보이스 × 포맷) + 타깃 회사 컬처 + 추천 이유 + 리스크.

---

### 추천 1 — **결정 로그 산문** (V7 분석톤 + V1 후과추적 아크 × F1 산문단락)

- **조합**: 직무당 2~3 산문 단락(F1) 안에 '담백 분석 톤(V7) + 물려받은 상태→판단→시차결과→**지속성/자기폐기**(V1)' 아크를 녹인다. `판단:` 라벨 제거, 흐름 자체가 판단이 되게.
- **타깃 컬처**: **채널톡(판단력·데스크앱)** 1순위, **토스플레이스(웹↔하드웨어)**. 판단·트레이드오프·후과추적을 가장 높이 사는 곳.
- **추천 이유**: 진완 최대 차별점('자기 결정의 후과까지 추적')은 불릿으로 못 담고 서사 아크가 필요한데, 이 조합이 정확히 그 아크다. 영문 예시 누구보다 진완이 강한 4번째 축(지속성/자기폐기)에서 '다름'이 나온다.
- **리스크**: ATS 파싱 약함 → F5 듀얼트랙으로 ATS본 분리. 산문이 길어지면 V5 인과체인으로 골격만 잡아 압축.

---

### 추천 2 — **정의문 헤더 + 인과체인 본문** (V3 콜론정의문 + V5 인과체인 × F2 깔때기 헤더 + F1 본문)

- **조합**: 최상단 = `저는 ~하는 일을 합니다:` 콜론 정의문(V3) → 깔때기(F2). 본문 카드 = 'from X to Y' 인과 체인(V5) 산문. 수치가 분명한 카드는 자연어 before/after로.
- **타깃 컬처**: **라인(결제·글로벌·AI)**, **오늘의집(품질자동화·집요함)**. 수치·집요함을 중시하므로 인과체인에 검증가능 대비를 실어 임팩트↑.
- **추천 이유**: 헤더 한 줄로 포지셔닝을 못박고(라벨 나열 탈출), 본문은 '눈검수→자동검출' 같은 검증가능 대비로 과장 없이 강하게. 픽셀 차분 회귀검출·관측 표준이 이 틀에 딱.
- **리스크**: 정의문이 추상적이면 buzzword가 됨 → 콜론 뒤를 반드시 구체 기술(Electron·Custom Protocol·LNA)로 받칠 것.

---

### 추천 3 — **작동방식 정체성 + 정직한 한계** (V4 한계먼저 + V6 정성임팩트 × F3 영문헤더 + F4 litany)

- **조합**: 섹션 헤더 `Bridge. / Quality. / Scope.`(F3) + 가치 litany 블록(F4). 본문은 '낡은 형태→새 구조, 누구의 고통 감소'(V6) + 자기폐기 정직 한 줄(V4).
- **타깃 컬처**: **채널톡**(솔직-판단 서사 비중↑), 개인 사이트/노션 이력서 헤더 공통.
- **추천 이유**: 제품 수치가 약한 진완 약점을 V6 정성임팩트가 정면 해결하고, V4 한계먼저가 미들급을 시니어처럼 읽히게 한다. 넓은 역할을 '경계를 메우는 한 작동방식'으로 응집.
- **리스크**: Paco/Maggie식 '수치 0' 극단은 진완 B2B 자산을 죽임 → litany는 헤더만, 본문엔 핵심 수치 1~2개 유지(하이브리드). 경어체 충돌 위해 '~다' 단문은 litany 블록 한정.

---

## 4. 샘플 한 입 — 추천 1번 조합으로 쓴 Linker 카드 도입부

> 보이스: V7 담백 분석톤 + V1 후과추적 아크 / 포맷: F1 산문 단락.
> **사실 가드레일 준수**: CAM 12종 · LNA · Custom Protocol · 20여 방안 · 로컬서버 폐기. 웹 빌드 수치/6분/인스톨러 수치 없음.

---

**Dentbird Linker — 웹↔로컬 CAM 디바이스 브리지**

Dentbird에서 만든 보철 케이스를 외부 CAM 소프트웨어로 보내려면, 사용자가 파일을 내려받아 압축을 풀고 CAM에 직접 투입해야 했습니다. 자체 로컬 서버가 없는 CAM은 그마저도 연동되지 않았고, 그 사이를 메우려고 시작한 게 Linker입니다. 처음엔 빠른 연동이 우선이라 앱 안에 로컬 서버(Express)를 띄워 브라우저와 통신하게 했습니다.

그런데 Chrome이 LNA(Local Network Access) 정책을 도입하면서 브라우저→로컬 통신 자체가 막혀, 이 방식이 동작을 멈췄습니다. 권한을 허용하면 되긴 했지만 작은 권한 팝업을 놓치는 사용자가 많아 CS 문의가 늘었습니다. 저는 LNA를 우회할 20여 방안을 펼쳐놓고 비교했고 — 가장 빠른 WebSocket은 머지않아 같은 정책에 다시 막힐 단기 해법이라 판단해, 느리지만 정책에 다시 막히지 않는 Custom Protocol을 택했습니다. 브라우저가 앱을 깨워 세션을 위임하면, 앱이 클라우드에서 파일을 직접 받아 변환한 뒤 CAM을 실행하는 구조입니다.

이 전환으로 원래 Linker의 핵심이던 Express 로컬 서버는 더 이상 쓰지 않게 됐고, 제가 처음 깐 토대를 제 손으로 걷어낸 셈이 됐습니다. 이렇게 만든 브리지 위에서 좌표계·전달 방식이 제각각인 CAM 12종을 단일 변환·연동 인터페이스로 추상화했고, 출시 이후 안정화와 연동 확장을 2026년까지 단독으로 책임지고 있습니다.

---

*(체감 포인트: `판단:` 라벨이 한 번도 안 나오는데 둘째 단락 전체가 판단이다. '제가 처음 깐 토대를 제 손으로 걷어냈다'가 진완 고유의 4번째 축 = 후과추적/자기폐기. 수치는 '12종' 하나만, 나머지는 'from 로컬서버 into Custom Protocol' 구조 대비로 임팩트를 세웠다.)*

---

## 부록 — 평가 루프 보강 (tonghuikang 루브릭 이식)

EVALUATION-RUBRIC.md에 4줄 추가 권장 (단 'assume numbers' 조항은 진완 Hard No와 충돌하므로 **제거하고 채택**):
1. specific — 정확히 어떤 시스템/내 정확한 기여인가
2. objectively measurable — 객관 측정 가능한 사실만인가
3. no 빈말 — 'effectiveness/efficiency/효율 개선' 같은 측정불가 표현 없나
4. one bullet = one accomplishment — 한 불릿=한 성취인가

## 부록 — 정직성 메모 (verbatim 확보 상태)

- verbatim 확보(fetched=true): V1·F1(ResumeProfessionalWriters), V2(Brittany Chiang), V3·F2(Geoffrey Litt), V4(Maggie Appleton), V5(localjo), V6(notchresume), V7(Dan Luu, Julia Evans), F4(Rauno Freiberg), F3(Wonny·정종윤).
- verbatim 미확보(2차 출처만, 발췌 창작 안 함): Pragmatic Engineer/The Tech Resume(페이월), Teal narrative(HTTP 403). 인용에 직접 쓰지 말 것.
- 한국어 적용 전제: 영문 예시 voice는 1인칭(I/We)이나 진완은 경어체('~했습니다', 룰 6장). 'voice'는 영문 1인칭 그대로가 아니라 **인과 접속(~라 / ~던 차에 / 운영 중 ~를 겪어)으로 잇는 산문 골격 + 맥락→판단→시차결과→지속성 아크**를 경어체로 이식하는 것.

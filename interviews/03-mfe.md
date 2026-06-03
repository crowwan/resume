# 03. MFE — 공통 모듈 통합 (iframe → 빌드타임 → iframe) — 4축 인터뷰

> 인터뷰 일자: 2026-06-03
> Status: done (대안 검토·iframe 비용 확인 완료 / 본문 작성 시 ③단계 기여 주체 분리 필수)
> 입력원: [findings/03-mfe.md](../findings/03-mfe.md) (사용자 히스토리 + module-monorepo/embed-modules 코드 + Confluence 2건 + git)

## 사실 정보 (Facts)

- **기간**: 초기 module-monorepo 약 5개월 집중(2024-11-21 ~ 2025-04-04, 본인 커밋 55). 이후 embed-modules 이관·iframe 회귀로 이어짐.
- **역할**: 초기 4개 공통 모듈(setting·export·explorer·viewer)의 **iframe 런타임 통합을 주도**. console-client Module Federation 설정 담당. embed-modules 이관 관여.
- **주요 기술**: NX 모노레포, iframe + postMessage(typed, same-origin), COEP/CORP/COOP 헤더, Module Federation(console-client), Webpack→Rspack, vite 멀티 엔트리, iwtk(VTK)→Three.js(cloud-mesh-io).
- **특이 사실**: 통합 전략이 **iframe → 빌드타임 → iframe**으로 진동. 각 전환이 조직 개편·모노레포 통합·배포 전략 변화와 맞물림.
- **⚠️ 기여 주체**: ①초기 iframe MFE = 본인 주도. ③최종 VTK→Three.js+iframe 결과보고서(번들 -57% 등)는 **타인 작성/주도** → 정량은 팀 맥락으로만 인용.

---

## 1. WHY — 문제 실체

(사용자 설명 원문 취지)
초기엔 cloud/crown/modeler/milling이 도메인도 관리팀도 분리돼 있었다. 그런데 공통 기능 4개(setting·export·explorer·viewer)가 있었고 — setting·export는 전 서비스 공통 dialog, explorer는 cloud 제외, viewer는 cloud 전용인데 mesh 렌더링에 크라운팀이 관리하는 사내 vtk 라이브러리 `iwtk`를 썼다. iwtk가 공통이라 업데이트하면 viewer·export도 같이 올려야 했다. **그래서 이 4개 기능에 변경이 생기면 각 서비스에 같은 수정을 반복하고 모든 서비스를 배포해야 하는 번거로움이 있었다.**

---

## 2. HOW — 트레이드오프

**Q2-1. 왜 iframe 런타임 통합이었나요? 다른 선택지는?**

(사용자 원문 취지)
- 기존에 **다른 컴포넌트들을 모듈화해 라이브러리로 배포**해봤는데, 이렇게 하니 매번 package 업데이트가 필요했고, 수정이 생기면 각 서비스가 버전 올리고 다시 배포해야 했다. **우리가 풀려던 문제(반복 수정·배포)를 그대로 남기니 라이브러리 배포는 선택사항이 아니었다.**
- 그래서 찾은 게 **런타임 통합**이었고, 여러 방식 중 **제일 빠르고 복잡하지 않게 접근할 수 있던 게 iframe**이었다.
- iframe 도입엔 인프라 설정이 좀 필요했다(정확힌 기억 흐림 — header 설정 류). → 코드 확인: COEP `require-corp`/COOP/CORP 헤더 + referrer 화이트리스트 검증.

**Q2-2. Module Federation은 검토했나요?**

(사용자 원문 취지)
- **이 작업 말고 잠깐 진행했던 notification 기능에서 Module Federation을 도입해봤다.** 그런데 **초기 설정이 복잡하고, 모노레포 형식이 아닌 경우 소비처(consumer)에서도 추가 설정할 게 복잡**해서 (이 모듈 통합의) 선택지에서 제외했다.
- 다만 **console-client라는 다른 서비스에는 일부 Module Federation이 적용돼 있고, 이 설정은 내가 진행했다.**
- → 즉 MF를 몰라서가 아니라 **써보고 트레이드오프로 뺀** 것. (코드 확인: `console-client/src/f-shared/lib/mf/`)

**Q2-3. 그런데 왜 빌드타임 통합으로 한 번 바꿨다가, 다시 iframe으로 돌아왔나요?**

(사용자 원문 취지)
- 4개 앱이 dentbird-solutions라는 **하나의 모노레포로 합쳐지면서** 빌드에 포함시키는 게 한결 쉬워졌다. 그래서 처음 이관할 땐 iframe을 그대로 들고 오지 않고 **빌드타임 통합**을 택했다. **당시 배포해야 하는 앱이 너무 많았던** 것도 배경이다. (관련 문서 레포 내 존재)
- 중복 존재 문제는 모노레포로 해결됐고, "모듈 변경 시 전 앱 배포" 문제도 조직 개편·레포 이관 + **모든 앱 통합 배포 전략**으로 (당시엔) 문제가 아니었다.
- 그런데 **통합 배포 전략을 도입하고 시간이 지나보니 통합 배포가 실제로 된 적이 많이 없었고**, 모듈이 바뀌면 어쨌든 각 앱이 다 배포돼야 하는 건 불편했다. 게다가 **사내 배포 프로세스가 짧은 주기 배포가 어려운 방향으로 바뀌면서** 배포 부담이 커졌다. 이 문제로 **다시 iframe 기반 런타임 통합으로 변경**했다.
- 이때 바뀐 점: 초기엔 모듈별 도메인(`module-explorer.dentbird.com`)이었는데, **현재는 cloud 도메인 하위로 빼서 `ai.dentbird.com/cloud/module.html`로 서빙**한다. (이 작업은 본인이 한 게 아님 — same-origin이라 CORS 제거·인증/쿠키 공유·postMessage origin 검증 단순화 이점.)

---

## 3. RESULT — 결과

**Q3. 실제로 얼마나 나아졌나요?**

- 본인 단독 기여(초기 iframe MFE)의 정량 지표는 남아 있지 않음 — **추정 금지.** 정성: 4개 모듈 변경을 각 서비스에 반복하던 것을 모듈 한 곳 수정으로 좁힘.
- ③단계(VTK→Three.js + 최종 iframe, **팀 결과/타인 주도**) 정량 [Confluence page/2282094594]:
  - cloud-desktop 배포 번들 **119MB → 51MB (-57%)**, iwtk WASM/리소스 68MB 제거
  - crown-client JS 번들 **17.61MB → 15.16MB (-13.9%)**
  - cloud-desktop iwtk 직접 import 2→0, `download-iwtk` 빌드 스텝 제거(CI 단순화)

**Q3-후속. 누가 효과를 봤나요?**

전 서비스 FE 팀(모듈 중복 수정·배포 감소), 사용자(초기 로딩 68MB 감소).

---

## 4. 지속성·한계 — 성장 서사

**Q4. iframe의 진짜 비용은 뭐였나요? (지금의 한계)**

(결과보고서 §6 + 코드 근거)
- **iframe 로딩 지연**(수백ms), **메모리 격리로 React/MUI 등 라이브러리 중복 로딩**, **postMessage File/Blob 전달 제한**(importTo 등 추가 설계 필요).
- 코드 통증: iframe이 별도 browsing context라 **폰트 CSS를 진입점에서 또 로드**, **auth를 iframe 전용으로 따로 초기화**(`initializeForIframeModule`).

**Q4-후속. iwtk→Three.js 전환은 왜 했나요?**

(사용자 원문 취지)
- **iwtk 의존성을 최대한 없애서 우리 팀이 이 모듈에 대한 제어를 최대한 가져오려고** iwtk → Three.js 전환을 진행했다. (크라운팀이 관리하던 iwtk vendor lock-in 해소 → `cloud-mesh-io` 라이브러리 신규 구축)
- ※ 이 전환은 **별도 findings 카드로 추가 발굴 예정**(사용자 요청).

**총평(성장 서사)**: 같은 문제(공통 모듈의 반복 수정·배포)를 두고 **라이브러리 배포 → iframe → 빌드타임 → iframe**까지 오가며, *정답인 기술은 없고 그때의 조직·배포 제약에 맞는 통합 전략이 있을 뿐*임을 체득. Module Federation도 써보고 뺀 판단.

---

## 면접 답변 스크립트 (2~3분)

> "초기에 우리 서비스가 cloud·crown·modeler·milling로 도메인도 팀도 나뉘어 있었는데, setting·export·explorer·viewer 같은 공통 기능이 있었어요. 이 4개가 바뀌면 각 서비스에 똑같은 수정을 반복하고 전부 배포해야 해서, 이걸 모듈화하는 작업을 했습니다.
>
> 처음엔 컴포넌트를 라이브러리로 배포하는 방식을 써봤는데, 결국 각 서비스가 버전 올리고 다시 배포해야 해서 풀려던 문제가 안 풀렸어요. 그래서 런타임 통합으로 방향을 틀었고, 그중 제일 빠르고 단순하게 갈 수 있는 iframe + postMessage를 택했습니다. Module Federation도 후보였는데, 마침 notification 기능에서 직접 도입해본 적이 있어서 — 초기 설정이 복잡하고 모노레포가 아닌 소비처에선 설정 부담이 커서 이 건엔 뺐습니다. 대신 console-client라는 다른 서비스엔 제가 Module Federation을 직접 적용했어요.
>
> 재밌는 건 그 다음이에요. 4개 앱이 한 모노레포로 합쳐지면서, 이관할 땐 iframe을 빌드타임 통합으로 바꿨어요. 빌드에 넣기 쉬워졌고 통합 배포 전략도 생겼으니까요. 그런데 막상 운영해보니 통합 배포가 실제로 잘 안 됐고, 사내 배포 프로세스가 짧은 주기 배포를 어렵게 바뀌면서 배포 부담이 커졌어요. 그래서 결국 다시 iframe 런타임 통합으로 돌아갔습니다. 이번엔 모듈별 도메인 대신 cloud 도메인 하위 same-origin으로요.
>
> 이 과정에서 배운 게, 통합 전략에 정답 기술은 없다는 거였어요. iframe은 로딩 지연이나 라이브러리 중복 로딩 같은 비용이 있고, 빌드타임은 배포 결합이 문제고 — 결국 그때의 조직 구조와 배포 제약에 맞는 선택을 하는 거더라고요. 최근엔 크라운팀이 관리하던 iwtk를 Three.js로 걷어내면서, 모듈 제어권을 우리 팀이 가져오고 번들도 크게 줄였습니다."

---

## 본문 반영 위치

- `career-description.md` § MFE / 공통 모듈 통합 (3단계 진동 + 대안 판단)
- `resume.md` § MFE 카드 (가장 강한 1~2줄: iframe 런타임 통합 주도 + 통합 전략 trade-off 판단)
- iwtk→Three.js / cloud-mesh-io는 별도 카드로 분리 예정.

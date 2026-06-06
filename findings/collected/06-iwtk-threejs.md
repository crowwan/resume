# iwtk/VTK → Three.js 전환 · 3D 뷰어 렌더링 정합 — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리
> 기존 findings/08-iwtk-threejs.md 에 이미 있는 사실(번들 -57%/-68MB, crown-client -13.9%, gamma 0.29 중간단계, jwkim 24커밋 한줄요약)은 제외. 그 위 레이어의 신규/보강 사실만 적재.

## 본인 기여 식별 요약
- **[본인 단독 주도]** — git author=jwkim@imagoworks.ai / jira assignee=jwkim / vault 본인 작성으로 교차확인:
  - 색/specular/채도/조명 정합 (DEN-4539, 본인 22커밋, PR #8484)
  - GENERIC UV draco 정공법 (PR #9432/#9404, ~1,420줄 제거)
  - ColorManagement 정책을 앱 entrypoint로 격상 (PR #9124)
  - abutment 별도 렌더 채널 설계 (DEN-3706, 본인 17커밋, PR #10301/#10620)
  - viewer mesh 변환 단일 진입점 `buildViewerModelItems` (ADR + PR #10301 follow-up)
  - 모델색/preference scope 정합 (CRWN-3294 #10658, DEN-4597 #10673/#10699)
  - DEN-4194 썸네일 오로라 결착 + 썸네일 visual regression 인프라 (PR #10428)
  - z-fighting displacementMap 재설계 (PR #10797/#10854)
  - 썸네일 material 단일화 (CRWN-3397 #10851, WV-008 #10874)
  - Three.js mesh visual regression CI 토대 (PR #9517)
  - VTK 썸네일 Three.js 통합 보류 ADR(본인 단독 실측·결정)
- **[팀-hckim(Hyeoncheol Kim) 주도, findings 08 기존]**: cloud-mesh-io 신규 구축 + ViewerDialog VTK→Three.js 전환 골격 + 결과보고서(Confluence 2282094594) + CRWN-3355 v6 preference cutover. → 본인 작업은 그 **위 레이어**(렌더 정합·회귀 안정화·SoT 통합·진단 워크플로).
- **[기여확인 보정]**: Confluence 2282094594 = 팀 결과보고서(작성자 hckim). 거기 PR 목록(#6485~#6503)은 전환 골격이고 본인 핵심 PR(#8484·#9124·#9432·#10301·#10620 등)은 그 이후 별도 시리즈 — 즉 Confluence는 본인 작업의 "전사(前史)". [confluence: 2282094594 §8 author=ba204367]

## 1. 의사결정 흔적

### 커밋·PR 발굴 (findings 08에 없던 결정 레이어)
- 색/조명 정합 핵심 결정 = "감마 보정 회피 → SRGB 정공법". `outputColorSpace=SRGBColorSpace` 전환 + 추가 gamma 제거(0.29→1.0). SRGB 위 추가 gamma → saturation 이중 wash out(light pastel root). [git: 본인 e905f2e3d20] [PR #8484]
- lighting VTK canonical ratio 매핑: `setupDentalLighting` ambient=0.3 / headlight=1.3(sum 1.6, ambient 23%). VTK 다광원→Three.js 단광원 의도적 over-scale. [git: 본인 e905f2e3d20]
- design 메쉬 specular=0 분기(VTK `DefaultModelColorSetBase.specular=0` 매칭). non-design 0x333333 유지. [git: 본인 697e6d86f34]
- 렌더링 우선순위 iwtk 정책 반전: vertex color > texture > group color. [git: 본인 4ac8d3f7e43]
- GENERIC UV 정공법: main-thread Draco WASM(540줄) 폐기 → `dracoLoader.defaultAttributeIDs.uv='GENERIC'` mutate. [git: 본인 2a62abadf66 / c20f111dfb6] [PR #9432, #9404]
- ColorManagement 정책 책임을 라이브러리→앱 entrypoint(4앱 main.tsx)로 격상. [git: 본인 8b3b14f8908] [PR #9124]
- abutment "표시 전용 별도 렌더 채널"(abutmentEntries, pontic 패턴) 설계. [git: 본인 0cb62c09209 / d4433c67923] [PR #10620, #10301]
- 렌더 셋업 cloud-mesh-io 단일 SoT 통합(3앱 호출부 변경 0 공유). [git: 본인 50533d3757e / 8b3b14f8908] [PR #10854, #9124]

### 문서(vault)에서 새로 확인된 결정 (커밋 발굴에도 없던 추가 신규)
- **viewer mesh 변환 단일 진입점(SoT) `buildViewerModelItems` 를 정식 ADR로 박제**. prefetch path / fresh fallback / 향후 추가 path 전부 단일 함수 통과. 트레이드오프 표(옵션 A 최소변경/SoT 미보장 ❌ vs 옵션 B SoT 통합 채택 ✅ vs 옵션 C batch prefetch 추가 ❌)까지 명시. 사내 코드룰 `bug-fix-regression.md`("다중 상태 N개면 단일 모델로")·`complex-ui-state.md`("같은 서버 데이터를 뷰별 다른 변환" 안티패턴)와 정합. [vault: decisions/2026-05-28-viewer-mesh-sot-buildviewermodelitems.md] [git: 본인 38eaee797e0] [PR #10301 follow-up]
- **VTK 썸네일 엔진을 Three.js로 통합하지 않기로 한 정식 ADR(책임경계 공존) — 본인이 실측 근거로 통합 보류 단독 결정**. 사용자 "통합하면 코드/성능 이점 있나" 질문 → 본인 실측: ① 번들 제거 0(VTK는 modeler/crown/milling 핵심엔진 잔존) ② 성능 이점 없음(저장 후 재캡쳐 시 mesh 재다운로드 왕복으로 오히려 악화) ③ LOC 감소 미미 ④ 유일 실익(drift 근절)은 정책 SoT 공유(소공사)로 회수 가능 → 통합 대공사 기각. "생성=VTK 권위 / 사후 재캡쳐=Three.js SDS 정합" 책임경계 결론. [vault: decisions/2026-06-05-vtk-thumbnail-three-migration-deferred.md]
- **z-fighting 해법을 4안 비교 후 (d) displacementMap 으로 채택 — 명령형→three.js 제공 메소드 전환**. 사용자 피드백("명령형 inflate 말고 three.js 제공 메소드로") 수용해 GLSL/명령형 vertex 루프/geometry mutation 없이 1x1 white DataTexture + displacementScale=eps(0.01)만으로 base/die normal 균일 inflate. 4안 정확도/GLSL/geometry 영향 트레이드오프 표로 정리. [vault: learnings/2026-06-04-viewer-coincident-zfighting-gl-fragdepth-renderorder.md] [PR #10797, #10854]
- **mesh-color preference scope를 host+solution 으로 확정(SDS 충돌 해소)**. P009(Cloud) vs P004(Batch) SDS 충돌(Batch embed viewer가 Crown룰? Batch룰?)을 `scope = exactHost==='batch' ? 'BATCH' : solution==='MODELER' ? 'MODELER' : 'CROWN'`로 해소. [vault: bugs/DEN-4597/investigation.md §Fix] [PR #10673, #10699]
- **모델색 정책 = White #FFFFFF 로 SDS 근거 확정 후 코드 교정**. 기존 `DEFAULT_GROUP_COLORS[MODEL]=#f4d89b`가 P009 SDS 5.3.1.8.4("Model→White #FFFFFF") 위반임을 확인 → `resolveGroupColor`에 MODEL carve-out(isDesign보다 우선) 추가. [vault: bugs/CRWN-3294/investigation.md] [PR #10658]
- **마이그레이션 후 잠복결함(invisible-then-surface) 대응을 패턴으로 정립**: 마이그레이션 자체보다 "그동안 invisible했던 결함의 surface 인벤토리"가 본질 위험 → ① 마이그레이션 전 invariant PR 선행 ② 마이그레이션 시 client fallback 안전망 ③ 이후 root cause fix 별 PR. WV-003(UV없는 mesh+texture 강제, 2~3년 묵은 결함)이 정확한 사례. [vault: learnings/2026-05-27-invisible-defect-surface-after-migration.md]

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

### 커밋·PR 발굴
- GENERIC UV 정공법 리팩토링 **~1,420줄 제거**(draco-generic 540줄 + 통합분기 ~80줄 등). [git: 본인 2a62abadf66] [PR #9432]
- cross-app 시각 회귀 가드 delta 정량: viewer `setupDentalLighting` 누락 시 pair **+57~+139%** / batch `outputColorSpace` 회귀 시 절대값 **0.89~5.29**(TOLERANCE 0.5 초과) / 정상 시 결정성 2회 PASS(variance ≤0.5). [git: 본인 5b718d81ade] [PR #9655]
- visual regression baseline: **4 case × 3 pair = 12 metric baseline**, ROI 640×720 center crop으로 3앱 canvas(cloud 932×780 / crown 1600×955 / batch 726×791) 흡수. [git: 본인 5b718d81ade]
- cloud-mesh-io 테스트 가드: design specular 분기 **84/84 PASS**, batch-web 유닛 **2635 통과**(abutment PR). [git: 본인 697e6d86f34 / 0cb62c09209]
- DEN-4539 본인 커밋 **22개**, DEN-3706 본인 커밋 **17개**(squash 전, PR 단위로는 더 적음). [git: 본인 grep count] [측정필요: 카드엔 PR 단위 표기 권장]

### 문서에서 새로 확인된 정량
- **VTK/iwtk 소비 실측(통합 보류 결정 근거, 2026-06-05)**: `@kitware/vtk.js` = **306 파일**(modeler-client 104 / crown-client 67 / milling-client 31 / crown-core-module 11), `@imagoworks-inc/iwtk.js` = **649 파일**(modeler 200 / crown 170 / crown-core-module 102 / milling 45). → VTK는 타 앱 핵심엔진이라 썸네일을 Three로 옮겨도 번들 감소 0. [vault: decisions/2026-06-05-vtk-thumbnail-three-migration-deferred.md verification]
- **CRWN-3397 썸네일 material 통일 = 순 -172줄**(3 material 함수 → applyThumbnailMaterial 단일 정책), batch-web 유닛 **2670 passed** + typecheck + lint. [vault: log.md 2026-06-05 CRWN-3397] [git: 본인 b547754df24]
- **CRWN-3294 모델색 fix 가드**: cloud-mesh-io **159 passed**(crown-export-baseline 포함), e2e mesh-visual `CRWN-3294 — Model(Die) isolate` case + crownWithModeler fixture baseline 2회 안정. [vault: bugs/CRWN-3294/investigation.md AC] [PR #10658]
- **DEN-4194 썸네일 오로라 fix = 1줄**(`.filter((m) => m.viewProperty?.isVisible !== false)`) — hidden mesh 13개(isVisible=false inner/outer/abutment)가 scene 합류해 IWTK MeshPhotographer texture/vertex 분기 race로 aurora. RED PNG **57858 bytes**(aurora) → GREEN **54652 bytes**(깔끔), 5일 작업 결착. [vault: log.md 2026-05-28 DEN-4194] [PR #7561(1차 test-lock), merge 15dab98de7b]
- **DEN-3706 SoT 통합 가드**: **156 spec PASS / 1129 tests / lib typecheck 0**. DomainTest 11 PASS. [vault: decisions/2026-05-28-viewer-mesh-sot-buildviewermodelitems.md] [git: 본인 38eaee797e0]
- **마이그레이션 잠복결함 age 정량(WV-003)**: 결함 도입 2022-07-05(`hasTexture.ts` 6822fd69c32) + 2023-12-07(`importFiles.ts` 4dd4aefb964) = **2~3년 묵은 결함**이 2026-05 Three.js 마이그레이션(DEN-4539 #8484)에서 surface. [vault: learnings/2026-05-27-invisible-defect-surface-after-migration.md]
- **abutment scan-fallback 색 차이 = 주로 B 채널**(design `#fff4e9` B=233 ↔ scan `#FFF7C7` B=199). [vault: learnings/2026-05-29-abutment-design-color-and-viewer-canvas-pixel-guard.md]
- **z-fighting depth 정량(VTK 메커니즘 분석)**: VTK `gl_FragDepth = gl_FragCoord.z + cfactor·slope + 0.000016·coffset`(0.000016 = 1/65536 = 16bit depth precision), iwtk `setRelativeCoincidentTopologyPolygonOffsetParameters(1, -50)` ≈ depth -0.0008 앞. [vault: learnings/2026-06-04-viewer-coincident-zfighting-gl-fragdepth-renderorder.md verification]

## 3. 후속 영향

### 커밋·PR 발굴
- `VTK_COLOR_MATCHING_GAMMA` 수명: 본인이 SRGB 정공법으로 가며 **0.29→1.0 무효화**. findings 08의 gamma 0.29는 본인이 폐기한 중간단계. [git: 본인 e905f2e3d20]
- specular/lighting 사용자 피드백 evolution: design 색 #ffffff→#fff4e9, non-design specular 0x333333→0x666666, fallback #fff4e9→#F4EFE5(P009 정합). [git: 본인 e905f2e3d20 / 908a674e290 / 22374e2f49b]
- 두 렌더 엔진(VTK 생성 / Three.js 재캡쳐) 규칙 drift 반복 → 썸네일 material `applyThumbnailMaterial` 단일화. [git: 본인 828acca9529 / 0d9d9183728] [PR #10851, #10874]
- z-fighting 정책 진화: ModelLayer 분류(renderOrder=0+polygonOffset) → 옛 `renderOrder -10 + polygonOffset +2` 제거 → displacementMap 재설계. [git: 본인 a42479fdc1d / 71b8f5fef1a / 50533d3757e]
- 모듈 경계 격차가 색 버그 반복 원인: embed-modules helper에 isDesign/groupLabel 인자 부재 → design crown이 scan색(#FFF7C7)으로 렌더 → options object 통일. [git: 본인 2cffd111bbc] [PR #8484]

### 문서에서 새로 확인된 트레이드오프
- **"같은 색 버그가 여러 번 다른 root로 재발"한 가족사 규명** — 색이상 family(DEN-4385→DEN-4549→DEN-4597·CRWN-3294)가 각각 root가 완전히 다름: DEN-4597=preference cross-app reader 단절(writer가 CRWN-3355로 v6 scope=CROWN 이관했는데 reader는 레거시 `/account/preferences/experimental` 읽음, cutover PR이 embed-modules 0파일 변경), CRWN-3294=resolveGroupColor의 isDesign 우선+MODEL carve-out 부재. blameless 분석(cross-app PR이라 리뷰어가 단절 못 봄, 계약 가드가 주석뿐). [vault: bugs/DEN-4597/investigation.md, bugs/CRWN-3294/investigation.md]
- **DEN-4597 root cause를 사용자 dev 관찰로 2번 뒤집어 정정**: ①"reader가 v6 못 봐 항상 default" → ②사용자 "같은 샘플인데 적용/미적용 갈림" 관찰로 reader 비결정 가설 → ③사용자 실제 payload 확인으로 "서버가 payload에 default `#FFF7C7` 박기 시작 → `applyScanGroupColors:36 if(group.groupColor) return group` 가드가 default를 truthy로 보고 preference 차단"이 진짜 root. (안전장치: 추정 아닌 사용자 payload 검증으로 확정) [vault: bugs/DEN-4597/investigation.md ROOT 정정]
- **CRWN-3294는 회귀가 아니라 본인이 5/13 fix(908a674e290)에서 명시적으로 deferred한 미완 facet**(모델색 #FFFFFF vs #f4d89b 정책 미확정, CLOUD-3111/3112로 분리)을 QA가 재오픈. → 성장/정직 서사 소재. [vault: bugs/CRWN-3294/investigation.md For future Claude]
- **z-fighting 최종해(d displacementMap)의 잔여 한계 정직 기록**: grazing(silhouette 극가장자리 n·view→0)에서 depth차 소멸 → 얇은 윤곽 띠 이론 리스크는 (a)(c)(d) inflate 계열 공통. 완전 angle-무관은 (b) gl_FragDepth 뿐이나 GLSL+early-z 비활성 비용. eps 키우면 완화하나 실루엣 어긋남 trade-off. [vault: learnings/2026-06-04-...zfighting.md]
- **WV-007 z-fighting을 처음 잘못 진단했다가 재정의**: 초기 z-fighting(coincident)로 보고 polygonOffset 튜닝했으나, 진짜는 "model이 scan 위에 온전히 얹히는 occlusion"(VTK opaque depth buffer vs dental.ts transparent alpha-blend layering 패러다임 차이)이었음 — transparent+depthTest 환경에선 renderOrder/offset으로 occlusion 못 뒤집음. (정직 서사: 진단 정정) [vault: log.md 2026-06-04 WV-007]
- **이 작업의 실제 비용은 z-fighting이 아니라 "환경 삽질"이었다는 회고**(frontend-dev `--host=0.0.0.0` 누락→nginx 403, esbuild hex→decimal grep 함정 등 >8h, 대부분 회피 가능). [vault: learnings/2026-06-04-...zfighting.md §5]
- **CRWN-3397 texture 복원이 잠재결함을 표면화** → WV-008(batch Three.js 썸네일을 review viewer와 specular/saturation/camera 정합) 후속 발생. "흰색 가림막을 걷으니 그 아래 결함 surface" 연쇄. [vault: log.md 2026-06-05 WV-008] [PR #10874]

## 4. 운영 패턴

### 커밋·PR 발굴
- 렌더 회귀를 "회귀 가드 + 재현 case id" 한 쌍으로 수정(unit invariant + spec + dev 재현 UUID). [git: 본인 697e6d86f34 / 71b8f5fef1a / f327fc5ceb0]
- Three.js mesh visual regression CI 토대를 본인이 구축: playwright visual project, viewport 1600×1000, `--use-gl=swiftshader --disable-gpu-vsync`로 CI Linux↔local macOS GPU 차이 흡수, pngjs mean|Δ| per-channel `compareMeshRender` + 단위 5건. [git: 본인 fc3ed17eaea] [PR #9517]
- draco3d로 인코딩 변종 직접 검증(numAttributes=1 semantic=INVALID) → `geometry.hasAttribute('uv')` 가드 fallback. [git: 본인 f327fc5ceb0] [PR #10269]
- AI 코드리뷰(claude-code-review APPROVE, Aikido) 비차단 지적 후속 반영. [git: 본인 0cb62c09209 / 12cb99b01bd]

### 문서에서 새로 확인된 운영 패턴
- **case-thumbnail visual regression 인프라를 "재사용 자산"으로 설계(단발 fix 거부)** — DEN-3961/4130/4193/4194가 같은 오로라 회귀를 4번 close→reopen한 본질이 "case 썸네일(IWTK) layer의 visual regression 가드 부재"임을 진단하고, DEN-4194 fix를 단순 1줄로 끝내지 않고 격리 thumbnail e2e 인프라(Phase 1+2) + S3 graduate Stage 1+2로 확장. whitelist(closed set, 2 user-facing type) 전환으로 blacklist(open set 18 type) 대비 안정화. 사용자 manual 0 / production code 변경 ~13줄. PR #10428 release/2026-05 머지. [vault: decisions/2026-05-21-case-thumbnail-visual-regression-infra.md] [PR #10428]
- **production binary를 draco3d npm으로 직접 decode하는 mesh 진단 워크플로 정립**(dev mongo + Azure Key Vault + curl + draco3d, 15~20분에 가설→확정). semantic 해석 매트릭스(POSITION/NORMAL/TEX_COORD 정상 / GENERIC=DEN-4539 cascade / numAttributes=1 INVALID=WV-003 변종 / COLOR 검정). [vault: learnings/2026-05-27-draco3d-production-binary-attribute-dump.md] [PR #10269] [code: loaders.test.ts:152-281]
- **abutment 3D 렌더 회귀를 canvas 픽셀 가드로 검증(ModelTree DOM 검증 불가 인지)** — abutment는 P2 분리로 ModelTree 미노출이라 DOM으로 못 잡음 → `[data-testid="viewer-dialog-canvas"]` offscreen `drawImage`+`getImageData`로 design색(#fff4e9) 비율 검출. fixture 트릭: 정합만 완료·crown 미생성 case라 design색 픽셀=abutment 고유. cross-origin 못 읽으면 명시 실패(조용한 통과 금지). [vault: learnings/2026-05-29-abutment-design-color-and-viewer-canvas-pixel-guard.md] [git: 본인 e885ef76f58]
- **색상 회귀 가드를 유닛 미러링 대신 e2e(사용자가 보는 결과)로** — 사내룰 `test-no-source-mirroring.md` 적용(데이터 빌더 groupColor 미러링 금지). [vault: 위 동일]
- **bug fix를 거의 항상 vault investigation/decision/learning 3종 박제와 한 쌍으로 운영** — 결정 ADR, 트레이드오프 표, 안티패턴, "For future Claude" 재진입 가이드까지 문서화하는 패턴이 전 영역에서 일관(본인 작업 운영 방식의 강한 시그널). [vault: decisions/, learnings/, bugs/ 전반]

## ★ 기존 findings/이력서에 없던 새 증거 (이력서 차별화 후보)
- 색/조명 정합을 **gamma 우회(0.29) → SRGB 정공법으로 본인이 다시 풀어낸 것**(findings 08은 0.29를 결과로 기재 / 실제론 본인이 폐기한 중간단계). [git: 본인 e905f2e3d20] [PR #8484]
- **GENERIC UV draco 정공법: main-thread WASM 540줄 폐기 포함 ~1,420줄 제거**. [git: 본인 2a62abadf66] [PR #9432, #9404]
- **3앱 공유 Three.js mesh visual regression CI 토대 본인 구축**(swiftshader로 CI/local GPU 차이 흡수, 12 metric baseline, +57~+139% / 0.89~5.29 회귀 delta 정량). [git: 본인 5b718d81ade / fc3ed17eaea] [PR #9517, #9655]
- **viewer mesh 변환을 단일 진입점 `buildViewerModelItems` SoT로 통합한 ADR**(트레이드오프 3안 비교, 156 spec/1129 tests 가드). [vault: decisions/2026-05-28-...] [PR #10301 follow-up]
- **VTK 썸네일을 Three.js로 통합하지 않기로 한 실측 기반 보류 결정**(VTK 306파일·iwtk 649파일 소비 실측으로 "번들 감소 0" 증명, 책임경계 공존 ADR). — "안 하기로 한 결정을 데이터로 정당화"한 판단 서사. [vault: decisions/2026-06-05-...]
- **z-fighting을 명령형 inflate → three.js displacementMap(1x1 DataTexture)으로 재설계**(4안 비교, 잔여 grazing 한계 정직 기록). [vault: learnings/2026-06-04-...] [PR #10797, #10854]
- **같은 "색 버그"가 4번 다른 root로 재발한 가족사 규명 + blameless 분석**(preference cross-app reader 단절 vs resolveGroupColor carve-out 부재). [vault: bugs/DEN-4597, CRWN-3294]
- **마이그레이션 잠복결함(2~3년 묵은 invisible defect) surface 대응을 패턴화**(invariant 선행 → fallback 안전망 → root fix). [vault: learnings/2026-05-27-...]
- **case-thumbnail 오로라 회귀를 4번 reopen 끝에 visual regression 인프라(재사용 자산)로 근절**(fix 1줄, 인프라 production code ~13줄). [vault: decisions/2026-05-21-...] [PR #10428]
- **production draco binary를 npm draco3d로 직접 decode하는 mesh 진단 워크플로**(15~20분 가설→확정, semantic 해석 매트릭스). [vault: learnings/2026-05-27-...] [PR #10269]

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)
- ⬜ DEN-4539/DEN-3706 본인 커밋 수(22/17)는 squash 전 — 카드엔 PR 단위(#8484, #10301/#10620)로 표기할지 확인. [측정필요]
- ⬜ PR additions/deletions 정량은 Azure DevOps 머지라 `gh pr view`로 못 가져옴. -172줄·-1420줄·84/159/2635/2670 테스트 수치는 커밋 본문·vault 기재값 인용 — 이력서에 그대로 쓸지 확인. [측정필요]
- ⬜ Jira 본인 이슈가 D1-/DEN- 양 프로젝트로 split(예: D1-3106=DEN-4407) — 카드 표기 시 대표 키 하나로 통일할지. [jira]
- ⬜ Confluence 매칭은 팀 결과보고서 1건뿐(본인 작성 아님). 본인 작성 기술문서(batch-crown-generation-pipeline / thumbnail-vtk-vs-threejs)는 vault `_onboarding/`에만 있고 Confluence 미게시 — 이력서에 출처로 쓸 수 있는지. [vault: log.md 2026-06-05 doc]
- ⬜ "DEN-4194 5일 작업" 중 본인 단독 비중 vs 협업 비중 — 카드 정량화 전 확인. [기억]

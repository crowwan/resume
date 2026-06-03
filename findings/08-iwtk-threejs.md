# 08. iwtk(VTK) → Three.js 전환 + cloud-mesh-io — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (코드·Confluence·git 발굴 완료 / 본인 기여분 확인 필요)
> 발굴 대상: `dentbird-solutions/libs/cloud-mesh-io`, `embed-modules` ViewerDialog, Confluence 2282094594
>
> **★ 기여 주체** (2026-06-03 확정): 결과보고서·전반 구현은 hckim(35커밋) 주도이나, **본인도 cloud-mesh-io/Three.js 전환 설계에 참여** + **Viewer mesh 렌더 수정(24커밋, 색상/abutment/회귀가드)** 담당. → **08 개인 카드 유지.** 정량(번들 -57%)은 "팀 전환 결과"로, 본인 bullet은 "설계 참여 + Viewer 렌더 정합" 중심.

## 1. 의사결정 흔적
- **동기**: 크라운팀이 관리하던 사내 vtk 라이브러리 **iwtk(VTK.js 기반, 상용) vendor lock-in 해소** → 업계 표준 Three.js로 전환해 **모듈 제어권을 우리 팀이 확보** + 생태계 활용. [기억] + [Confluence: page/2282094594 §5]
- **신규 라이브러리 cloud-mesh-io**: Three.js 네이티브 메시 I/O(DRC/STL/PLY/OBJ/VTP 로딩·내보내기, DentalCameraControls, setupDentalLighting, VTK 시각 동등성). [code: libs/cloud-mesh-io]

## 2. 측정 가능한 변화 (★ 정량 — 팀 결과)
- cloud-desktop 배포 번들 **119MB → 51MB (-68MB, -57%)**. [Confluence §2]
- **iwtk WASM 런타임 ~35MB + iwtk 리소스 68MB 완전 제거** → 초기 로딩 68MB 다운로드 사라짐.
- crown-client JS 번들 **17.61MB → 15.16MB (-13.9%)** (Dialog 트리 iframe 분리).
- cloud-desktop iwtk 직접 import 2→0, `download-iwtk` 빌드 스텝 제거(CI 단순화).

## 3. 후속 영향 / 트레이드오프 (잃은 것)
- **렌더링 차이**: VTK vs Three.js 파이프라인 차이로 pixel-perfect 불가 → 실험적 감마 `VTK_COLOR_MATCHING_GAMMA = 0.29`로 시각 동등성 확보(일부 vertex color 미세 차이). [Confluence §6]
- **WebGL 컨텍스트 관리**: Three.js offscreen renderer 싱글톤(브라우저 한도 8~16개), `dispose()` 책임.
- **iframe 오버헤드**: 로딩 지연 수백ms + React/MUI 중복 로딩(메모리 격리).
- **iwtk 잔존**: crown-client(CAD 코어, 범위 밖) + embed-modules 7개 파일(WebDesignProcessor 등).

## 4. 운영 패턴 / 기여 주체
- **[팀-김현철 주도]**: cloud-mesh-io 신규 구축, ViewerDialog VTK→Three.js 전환(PR #6485~#6503, Phase 0~5). 결과보고서 작성(authorId ba204367).
- **[본인 일부]**: jwkim 24커밋 — `[DEN-4597] Batch/Cloud Viewer Model(Die) mesh 색상 수정`, `[DEN-3706] Batch Viewer implant abutment 표시 + 색상 통일` 등 **Viewer 렌더 정합·회귀 수정**. [git: 본인]
- 다른 기여자: sihyeong lee(29), Songyi Jung(9), Jihoon Park(8). [git]

## 확정/미해결
- ✅ 본인은 **Three.js 전환 설계에도 참여** + Viewer 렌더 수정. (2026-06-03)
- ⬜ Viewer 색상/abutment 수정 작업의 임팩트(어떤 버그·회귀를 잡았나) — 카드 작성 시 정성. [기억]

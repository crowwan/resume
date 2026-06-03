# 07. Design System (imago-design-system, MUI 기반) — 발굴된 사실

> 수집 기준: [README.md](./README.md)
> Status: partial (코드·Confluence·git 발굴 완료 / 본인 기여 범위 확인 필요)
> 발굴 대상: `~/Works/devops/imago-design-system`, Confluence 1940946953·2162393237
>
> **기여 주체**: 본인 기여는 **DatePicker 확장 등 부분 기여(10커밋, 2025-04~08)**. 디자인 시스템 전체 설계/마이그레이션은 팀 다수.

## 1. 의사결정 흔적
- **목표**: Design QA 에러 감소(디자인↔개발 싱크 불일치 해소), MUI 공식 문서/예제 직접 활용, 커스텀 래퍼 제거로 유지보수 비용↓. [Confluence: page/1940946953]
- **핵심 원칙**: "MUI를 커스텀하는 게 아니라, **MUI가 제공하는 커스텀 시스템을 사용**한다." [Confluence]
- **기술 선택**: MUI v5.11.9 + Emotion + `createTheme()` API + TypeScript + Storybook v7. [code: imago-design-system/package.json, src/config/theme.ts]

## 2. 측정 가능한 변화
- v2 → v3 메이저 마이그레이션 진행(현재 v3.0.0-28대, 2025-10 기준). 기존 커스텀 design-system 패키지 폐기 예정. [code/Confluence]
- 컴포넌트 분류: Inputs(DatePicker/TextField/NumberCounter/Select), DataDisplay(Chip/Label/Tooltip), Feedback(Alert/Toast), Navigation, Surfaces, Icons(CiIcon/RiIcon). [code: src/Components/]

## 3. 후속 영향
- Crown/Batch/Solutions 전 제품에서 공통 UI 기반으로 사용. [Confluence: page/2162393237]

## 4. 운영 패턴 / 기여 주체
- **[본인 일부]** (Jinwan Kim, 2025-04-23 ~ 2025-08-28, **약 10커밋**): DatePicker 확장(onClose props 덮어쓰기 버그 해결, placeholder 추가, onClick handler 노출), v3.0.0 릴리스 관리(3.0.0-13/15). [git: 본인]
- **[팀]**: Seongjoo Kim(아이콘·custom icon), Yeonho Jeong/PhilSung/Sihyeong Lee/Jihoon Park(컴포넌트 확장·버전 관리). [git]

## 미해결 (질문)
- 본인 기여가 DatePicker 등 부분 컴포넌트인지, 더 큰 범위(테마/토큰/마이그레이션 주도)가 있었는지. [기여확인]
- 디자인 시스템 도입으로 실제 Design QA가 줄었는지 체감/지표. [기억]

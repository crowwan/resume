# Solutions 플랫폼 인프라 (모노레포 통합·런타임 config·Build Once·빌드 최적화) — 추가 발굴 (collected)
> 출처 태그 강제 · 발명 NO · 본인(jwkim) 기여 분리 · (조회일 표시 생략)
> 기존 `findings/01-solutions.md`(런타임 config + 격리재현) 및 `findings/collected/04-electron-infra.md`(Electron 빌드·서명·blue-green·/tmp 장애·EC2 크론)에 **이미 있는 사실은 중복 적재 제외**. 여기엔 그 위에 **새로/보강된** 사실, 특히 **모노레포 통합·빌드도구·기여 주체 정정**만.
> 출처 태그 범례: `[git: repo@sha7]` · `[PR: repo#num]` · `[jira: KEY]` · `[confluence: pageId]` · `[vault: path]` · `[기억]` · `[측정필요]`. ★ = 기존 findings에 없던 NEW. ⚠️ = 기여 주체/카드 경계 주의.

---

## 본인 기여 식별 요약

- **본인(jwkim) 주도로 확인된 이 카드 영역**: ① cloud 앱·라이브러리 별도 레포 → 메인 NX 모노레포 **Git Subtree Split 이관** + 지속 동기화, ② **i18n 스크립트 중앙화 리팩토링**(중복 스크립트·산재 NX 타겟 정리), ③ cloud-desktop **Webpack→Rspack 전환의 본인 슬라이스**(libarchive `getPublicPath` Vite/Rspack 양립 util), ④ 모노레포/빌드 부산물 버그픽스(NX cache VERSION key, promote-fast html sync, non-buildable lib artifact 제거, cross-platform electron-builder 러너).
- ⚠️ **본인 기여 아님 — [팀-Hyeoncheol Kim]으로 출처 확정**: **웹 클라이언트 CI Build-Once / 빌드시간 -90% / runtime-config 문서·소유권**. Confluence `creator=currentUser()` 전수 조회 결과 jwkim 본인이 작성한 모노레포통합·GHA전환·runtime-config·Build-Once·CI빌드최적화 문서는 **0건**이고, 6개 핵심 인프라 문서 전부 author=Hyeoncheol Kim. `[confluence: CQL creator=currentUser; 2284322927/2284322880/2221735944/2358378605/2315944218/2290221058/2274394290/2328723662 author=Hyeoncheol]`
- ⚠️ **두 개의 self-hosted 러너 인프라 혼동 금지**: (A) **김현철의 Linux build-server**(192.168.0.230, AMD Threadripper 7960X 24c/48t·128GB, GHA runner 28개+Azure agent 동거, 웹/서버/PR-checks/배포 CI 전담) `[confluence: 2328723662 author=Hyeoncheol]` vs (B) **본인의 macOS/Windows Electron 빌드 러너**(`build-machine.local`, ARM mac, `imago.keychain-db`+Apple 인증서) `[code: ghactions-build-migration doc, git: jwkim]`. "self-hosted 러너 구축" 진술 시 본인 것은 (B).

---

## 1. 의사결정 흔적

### 모노레포 통합 (본인 owner) — 보강
- ★ [본인] cloud 앱·라이브러리(apps 2: cloud-desktop/cloud-mobile + libs 6)를 별도 레포에서 메인 모노레포로 **Git Subtree Split 방식 이관**. 폴더 네임스페이스 충돌 회피 리네임(`libs/core→cloud-core` 등). 이관 후 `git subtree pull` 지속 동기화. `[git: dentbird-cloud-client-monorepo, code: docs/migration/*, docs/migration/git-history-preservation-strategy.md]`
- ★ [본인] **i18n 스크립트 중앙화 리팩토링** — NX 모노레포에서 중복 i18n 관리 스크립트 통합. Upload 스크립트 2개 프로젝트 중복(89줄), package.json 14개 i18n 스크립트 산재, 프로젝트별 NX 타겟 분산 → `scripts/i18n/` 중앙화로 정리. **본인 personal space 자작 문서 = 본인 기여 확실.** `[confluence: 1700888768, author Jinwan, 2025-10-10]`
- ★ [본인] **non-buildable library build artifact 제거** — `webpack.config.js` 잔존이 NX 자동 타겟 추론 유발 → cloud-mobile 빌드 에러. 삭제로 **빌드 태스크 11개→9개 감소 + OOM 해결**. NX 자동 타겟 추론 함정 이해. `[jira: D1-701, assignee jwkim, 2025-11-12]`

### 빌드 도구 변천 (본인 슬라이스 + 정정)
- ★ ⚠️ **cloud-desktop 빌드 도구 변천 = Webpack → Rspack(본인, 2025-11-12) → Vite 8(팀, 2026-03-20)**. 본인 Rspack 전환은 **약 4개월 운영 후 팀 차원 Vite 8 통일로 대체됨**(김현철 Phase 1, 5개 클라 앱 Webpack/Rspack→Vite 8 마이그레이션, PR 32492). `[vault: decisions/cloud-desktop-rspack-migration.md; learnings/2026-05-21-vite-rspack-public-path-fallback.md; confluence: 2284322880 author Hyeoncheol]`
  - → 이력서에 쓸 경우 "성장/한계 서사"로만 정직하게(당시 TS7056 회피 목적 달성, 이후 팀 표준화로 Vite로 수렴).
- [기여확인] Rspack 전환 자체가 본인 단독인지 팀 공동인지 미확정 — vault participants "김진완 + in-repo team". **본인 확정 슬라이스 = `getPublicPath` util만.**
- ★ [본인] **libarchive worker URL Vite/Rspack 양립 `getPublicPath` fallback chain**(`import.meta.env.BASE_URL → __webpack_public_path__ → '/'`). MeditLink/Shining3D 공통 정본. 근본원인: cloud-desktop이 Vite 빌드라 `__webpack_public_path__` undefined → worker 404 silent hang. **vault learning + bugs 양쪽 박제 = 본인 기여 확실.** `[vault: learnings/2026-05-21-vite-rspack-public-path-fallback.md; git #9982 CRWN-3387 / #9945 CRWN-3488]`

> Electron 빌드 파이프라인 GHA 이관·코드서명·D1-2732/D1-3608·blue-green·universal/NAS 결정은 `collected/04-electron-infra.md`에 이미 적재 — 여기서 중복하지 않음. 본 카드와의 연결점만: Electron(B) 러너는 본 모노레포 위에서 동작하며 김현철 태그 컨벤션(`{env}/{slug}/{YYYYMMDD-HHmm}`)·NAS 폴더규칙을 **차용한 측**. `[confluence: 2315944218/2274394290 author Hyeoncheol]`

## 2. 측정 가능한 변화 (출처에 적힌 수치만)

### [팀-Hyeoncheol] 웹 파이프라인 — 본인 성과 아님, 맥락/면접 서비스설명용
- 클라 빌드 중앙값 345~523s → 40~60s (**-87~91%**), p95 1,100~1,600s → 70~108s (-93%), 6주(2026-03~04). `[confluence: 2284322880 author Hyeoncheol]`
- promote 시간 ~120s(full rebuild) → **~11s**(promote), 6 Phase. `[confluence: 2284322927 author Hyeoncheol]`
- cloud-desktop CI 전체 ~6분(hang)→~1.5분(-75%), pnpm install 193s→2s(-99% shared node_modules hardlink snapshot), build step 56s→4.5s(-92%). `[confluence: 2221735944 author Hyeoncheol]`
- 기여도 분해: CI 인프라(shared node_modules) ~45% / GHA 전환 ~25% / Vite 빌드 ~15% / IWTK 제거 ~10%. `[confluence: 2284322880]`
- Platform Server CI GHA: 서버 빌드+배포 2m10s~3m50s → 45~52s. `[confluence: 2290221058 author Hyeoncheol]`
- → **인용 시 반드시 "팀(김현철) 주도 인프라"로 귀속. 본인 것 아님.**

> 본인 귀속 가능한 정량(Electron self-hosted: macOS -20%/-45%/-66%, artifact 757MB→348/334MB, legacy-batch -56% 등)은 `collected/04-electron-infra.md` §2에 이미 적재.

### [본인 인프라 맥락] build-server 디스크 압박
- self-hosted build-server(김현철 Linux 머신) 디스크 100% full 재발 → 28러너 crash-loop. 복구 실측 ~30분. 100Mb/s 네트워크 협상(10GbE 보드인데) = I/O 병목. `[confluence: 2328723662 author Hyeoncheol]`
- → 본인 PR #10857(build-client `/tmp` tarball 정리, `collected/04-electron-infra.md` §4 상세)이 이 머신의 디스크 압박 계열 대응. **인프라 문서 자체는 김현철, 본인 기여는 build-client.yml tmpfs 누적 fix 한정.**

## 3. 후속 영향 (본인 버그픽스)

- ★ [본인] **NX cache 정합성**: `generate-maker-options` target `inputs` 미선언 → VERSION cache key 누락 → stale maker.options. `{ "env": "VERSION" }` 명시로 차단. `[git #7942]` (D1-2732의 linker-desktop VERSION env 미지원 수정과 연결.)
- ★ [본인] **promote-fast.yml html sync 회귀**: 멀티엔트리 `module.html` S3 stale → `aws s3 sync --include "*.html"`. `[git #9240]`
- ★ [본인] **GHA 이관 부산물 cross-platform 러너**: `run-electron-builder.sh`(bash) Windows pwsh→WSL `node: not found` → cross-platform `run-electron-builder.js`(Node). `[git #7884]`
- ★ [본인] Universal prepareElectron `npm rebuild better-sqlite3 --runtime=electron` 제거 → ~10-20s 단축. `[code: ghactions doc]`

## 4. 운영 패턴

- ★ [본인] mac/win self-hosted 러너(B) 부트스트랩: Win `LongPathsEnabled`/`core.longpaths`, PS 7.6.1, JDK 21(@nx/gradle graph 필수), Defender 예외, AutoLogon; mac 전용 `imago.keychain-db`+Apple 인증서. `[code: ghactions doc, .claude/knowledge/infra/build-machines.md]`
- ★ [본인] 방어장치: sparse-checkout(Win MAX_PATH 회피), Bootstrap PATH step, pnpm 401 fail-fast `[git #7689]`; runner-watchdog(LaunchAgent 60s polling), NAS 마운트 LaunchAgent, NAS copy 3회 retry+auto-remount `[git #7884/#7929]`.
- 릴리스 태그 운영 규모 1710개. `[git tag]`
- ★ [팀-Hyeoncheol, 맥락] 태그 네이밍 `{env}/{slug}/{YYYYMMDD-HHmm}`, retag 프로모션(재빌드 없이 manifest copy), NX affected 기반 PR-필터 릴리스노트 = 김현철 설계. 본인 Electron 파이프라인도 이 컨벤션·NAS 폴더규칙(`{app}/{version}-{env}/`) 따름 = 차용한 측. `[confluence: 2315944218/2274394290 author Hyeoncheol]`

## ★ 기존 findings/이력서에 없던 새 증거

1. ★ **[정정확정] 웹 Build-Once / CI 빌드시간 -90% / runtime-config 문서·소유권 = 김현철(Hyeoncheol Kim) 주도** — Confluence CQL 전수조회로 교차검증. 기존 `findings/01-solutions.md` A섹션의 "런타임 config 본인 핵심 설계 참여" 및 "CI 3배 감축"은 **웹 파이프라인 한정으로는 본인 성과 아님**. 본인 기여는 Electron(B) 트랙 + 모노레포 통합 + 특정 버그픽스로 한정. `[confluence: CQL creator=currentUser]`
2. ★ **모노레포 통합 = Git Subtree Split + 네임스페이스 리네임 + `git subtree pull` 지속 동기화**(본인 owner) `[git: dentbird-cloud-client-monorepo, code: docs/migration/*]`.
3. ★ **i18n 스크립트 중앙화 리팩토링**(중복 89줄·14개 산재 스크립트·분산 NX 타겟 → `scripts/i18n/` 중앙화) — 본인 자작 Confluence 문서 `[confluence: 1700888768]`.
4. ★ **non-buildable lib artifact 제거로 빌드 태스크 11→9 + OOM 해결**(NX 자동 타겟 추론 함정) `[jira: D1-701]`.
5. ★ **cloud-desktop 빌드도구 변천 Webpack→Rspack(본인)→Vite 8(팀)** — 본인 Rspack 전환이 ~4개월 후 팀 Vite 통일로 대체된 성장/한계 서사 `[vault + confluence: 2284322880]`.
6. ★ **libarchive worker `getPublicPath` Vite/Rspack 양립 fallback util**(worker 404 silent hang 근본해결, MeditLink/Shining3D 공통 정본) `[vault + git #9982/#9945]`.
7. ★ **NX cache VERSION key 누락 차단 `[git #7942]` / promote-fast html sync 회귀 fix `[git #9240]` / cross-platform run-electron-builder.js `[git #7884]`** — 모노레포·빌드 부산물 본인 버그픽스.
8. ★ **두 self-hosted 러너 인프라 분리**(김현철 Linux build-server vs 본인 mac/win Electron 러너) — "러너 구축" 진술의 귀속 명확화 `[confluence: 2328723662 vs code/git]`.

## 미해결 (사용자 확인 필요: [기억]/[측정필요]/기여 모호)

1. [정정 반영 필수] "런타임 config 본인 핵심 설계"(기존 findings A §36, [기억 2026-06-03 사용자 확정]) ↔ git+Confluence상 **웹 파이프라인은 전부 김현철**. 본인이 "설계 논의 참여"였는지 "코드 기여"였는지 **사용자 재확인**. 코드 기여 = linker dotenv override / NX cache VERSION / promote-fast html sync / cloud env 폴백패턴 한정으로 보임. `[기여확인]`
2. [측정필요→일부해소] 기존 findings의 "CI 3배 감축"(01-solutions A §20) 출처 = **김현철 웹 파이프라인 -90%**일 가능성 높음(본인 것 아님). 본인 정량은 Electron 한정. 사용자 확인.
3. [기여확인] cloud-desktop Webpack→Rspack 전환 **본인 단독 여부** — vault "김진완 + in-repo team". 본인 확정 슬라이스는 `getPublicPath` util뿐.
4. [측정필요] **모노레포 통합 자체의 CI 빌드시간/생산성 효과** — 어느 문서에도 정량 없음. 인터뷰 보강 대상.
5. [신규 발굴 가치, 확인 필요] **CSP 설정 문서(1582235878)**는 본인 자작 인프라 문서이나 **"[작성 중]" 상태** — 완료 여부·실제 적용 범위 사용자 확인 필요.

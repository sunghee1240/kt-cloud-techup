# kt-cloud-techup

KT cloud TECH UP(국비지원 실무 개발자 양성과정) 모집 랜딩페이지.
GitHub Pages로 배포: https://sunghee1240.github.io/kt-cloud-techup/
저장소: https://github.com/sunghee1240/kt-cloud-techup (main 브랜치 루트에서 Pages 배포)

## 구조 — 단일 파일, 빌드 없음

`index.html` 하나에 CSS(`<style>`)·HTML·JS(`<script>`)가 전부 인라인으로 들어있다.
프레임워크·번들러·패키지 매니저 없음 (package.json 없음). `assets/`만 별도 폴더:

- `assets/fonts/techup-ko.woff2` — 커스텀 한글 폰트(Techup, Pretendard 계열), `OFL.txt` 라이선스 동봉
- `assets/images/` — 과정별 비주얼(webp 1440/800 반응형 세트), 과정 결과물 이미지(webp+png 원본), 파비콘, OG 커버
- `assets/pdf/` — 과정별 RD(직무기술서) PDF 5종

**수정 원칙**: 별도 파일로 쪼개거나 빌드 도구를 도입하는 리팩토링은 요청받지 않는 한 하지 않는다.
지금 구조가 의도된 것이며, GitHub Pages에 그대로 서빙된다.

## 콘텐츠는 전부 JS `DATA` 객체 안에 있음

`<script>` 안 `const DATA = {...}` 하나가 5개 과정(프로덕트 디자인/풀스택/백엔드/프론트엔드/
프로덕트 매니지먼트)의 카피·커리큘럼·커리어 정보를 전부 담은 거대한 리터럴이다.
문구 수정은 이 객체 안 문자열을 직접 고치면 된다. 탭 렌더링, 폼, 라우팅 등 로직은
`DATA`를 읽어 innerHTML을 조립하는 순수 vanilla JS (해시 기반 섹션 전환, IntersectionObserver
애니메이션, `esc()` 로 XSS 이스케이프).

## 기수마다 갱신해야 하는 하드코딩 값 (가장 중요)

과정 개편이나 다음 기수 모집 때마다 아래를 반드시 확인/갱신:

- `DATA.common.applyDates`, `DATA.common.trainingDates` — 신청 마감일 / 교육 기간
- 각 과정 `officialUrl` — work24.go.kr 상세페이지 링크, `tracseId` 파라미터가 기수별로 다름
- 각 과정 `trainingSheet` 파일명 + `assets/pdf/*.pdf` 실제 파일 — RD 문서 갱신 시 같이 교체
- `TRAINING_SHEETS` 매핑 객체 (라인 161 근처) — 과정명→PDF 경로

## Google Forms 연동 — 여기 건드릴 때 특히 주의

리드 수집 폼은 Google Forms로 prefill-리다이렉트하는 방식이다:

```
GFORM = 'https://docs.google.com/forms/d/e/.../viewform'
F_NAME = 'entry.946896190'
F_PHONE = 'entry.353977509'
F_ELIGIBLE = 'entry.1117058926'
KAKAO = 'https://open.kakao.com/o/sAc0mSLd'
```

**Google Form을 새로 만들면 `entry.NNNNNN` 필드 ID가 전부 바뀌어서 prefill이 조용히 깨진다.**
기존 폼을 계속 재사용하거나(수정만), 폼을 새로 만들어야 한다면 새 폼 URL로 접속해
각 질문의 name 속성(`entry.xxxxx`)을 개발자도구로 다시 확인한 뒤 세 값을 전부 교체해야 한다.
제출은 폼 자체 제출이 아니라 새 탭에서 Google Form을 열어주는 방식(`openPrefill`)이므로
실제 값 검증(이름/연락처 형식)은 Form 쪽이 아니라 이 페이지의 JS(`document.addEventListener('submit', ...)`)가 담당한다.

## 접근성 / 다크모드 — 임의로 제거하지 말 것

- skip-link, `:focus-visible` 아웃라인, `prefers-reduced-motion` 대응(애니메이션 스킵)
- `prefers-color-scheme: dark` 자동 다크모드 (CSS 변수 재정의 방식)
- `<noscript>` 폴백에 신청 링크·카카오 링크 둘 다 포함되어 있음 — JS 로직 바꿀 때 폴백도 같이 확인

## 로컬 확인

빌드 없음. `index.html`을 브라우저로 직접 열거나 정적 서버(`python -m http.server`)로 띄워서 확인.
배포는 `main` 브랜치에 push하면 GitHub Pages가 그대로 서빙.

## 여러 PC에서 작업할 때

이 문서는 저장소 루트에 커밋되어 있다 — 회사/집 PC 어디서든 `git clone`/`git pull` 하면
이 CLAUDE.md가 같이 따라온다. 로컬에만 저장하는 메모는 만들지 말 것.

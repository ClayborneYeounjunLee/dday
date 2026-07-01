# 디데이 · D-Day — 공익(사회복무) 복무 계산기

> 사회복무요원(공익)의 **복무 D-day·진행률·앞으로 출근일수**를 계산하고, **병가 / 연가 / 특별휴가**를 캘린더 위에서 관리하는 단일 파일 웹앱입니다. 주말·대한민국 공휴일·대체공휴일을 제외한 **실제 근무일** 기준으로 "출국 전에 병가를 다 쓰고, 출국~소집해제 구간은 연가·특별휴가로 채우는" 조기전역 시나리오를 자동으로 짜 줍니다.

🔗 **라이브:** https://clayborneyeounjunlee.github.io/dday/
📦 **저장소:** https://github.com/ClayborneYeounjunLee/dday
🧩 **모아 허브 계열 앱** (앱바 `◈` 버튼으로 이동): https://clayborneyeounjunlee.github.io/moa/

---

## ✨ 주요 기능

- **핵심 통계 카드 4종** — ✈️ 호주 출국(조기전역) D-day, 🎉 실질 마지막 근무일, 📅 소집해제(공식) D-day + 진행률 바, 🧳 앞으로 출근할 근무일 수(주말·공휴일 제외).
- **조기전역 플래너** — 남은 연가+특별휴가 전량을 소집해제 직전에 "몰아 배치"하여 **실질 마지막 근무일**과 **완전 고정 휴가구간**을 자동 계산. 출국일이 이 휴가구간 안에 드는지도 검증(부족하면 경고 노트).
- **병가 소진 시나리오 표** — 병가 잔여를 하루 3h / 4h / 5h / 8h(전일)로 조퇴할 때 각각 **며칠 걸리는지·시작일**을 계산. 행을 누르면 캘린더에 해당 조퇴 구간이 하이라이트.
- **연가·특별휴가 커버 패널** — 출국 후~소집해제까지 채우는 휴가구간을 날짜·일수로 표시.
- **복무 현황 요약** — 근무상황관리 화면 기준 값(연가/병가/특별휴가 사용·잔여, 정상출근·군사교육 등)을 표시.
- **월간 캘린더** — 복무시작·소집해제·출국일 마커, 연가(파랑)/특별휴가(보라)/병가(빨강) 태그, 병가 조퇴구간(앰버 테두리), 주말·공휴일 음영, 공휴일 이름 표기. 날짜를 누르면 그 날 사용 기록 시트가 열림.
- **사용 기록(로그)** — 실제로 쓴 병가(시간)/연가(일)/특별휴가(일)를 기록하면 잔여에서 **자동 차감**되고 캘린더에 반영. 기록별 삭제 가능.
- **설정 시트** — 소집일·소집해제일·출국일·하루 복무시간·잔여 휴가·"토/일 근무" 및 "소집해제일 휴가 포함" 토글을 바꾸면 전체 계산·캘린더가 즉시 갱신.
- **다국어(한국어 / English)** — 앱바 `EN`/`한` 토글로 전환, 공휴일 이름도 한↔영 매핑.
- **라이트/다크 테마** — `hub-theme` 키로 모아 계열 앱과 공유, 시스템 선호(`prefers-color-scheme`)를 초기값으로. 로드 시 깜빡임 방지 인라인 스크립트.
- **완전 오프라인** — 모든 데이터가 이 브라우저 localStorage에만 저장(서버/계정 없음).

---

## 🧱 기술 스택 / 언어

| 항목 | 내용 |
|------|------|
| **언어** | 순수 **HTML5 + CSS + 바닐라 JavaScript** (프레임워크·라이브러리 0개) |
| **모듈 방식** | ES 모듈 아님. 일반 인라인 `<script>` 2개 (테마 선적용용 1개 + 앱 로직 1개) |
| **빌드 도구** | 없음. 트랜스파일·번들 없이 브라우저가 `index.html`을 바로 실행 |
| **CDN / 외부 라이브러리** | 없음 (외부 `<script src>`·SDK·fetch 호출 전무) |
| **폰트** | 웹폰트 로드 없음. 시스템 폰트 스택 `"Pretendard","Apple SD Gothic Neo","Malgun Gothic","Noto Sans KR",sans-serif` (설치돼 있으면 Pretendard 사용) |
| **아이콘 / 파비콘** | 인라인 SVG **data URI** 파비콘, UI 아이콘은 이모지 |
| **스타일 시스템** | CSS 변수 디자인 토큰(`:root` / `html[data-theme="dark"]`), `color-mix()`, `env(safe-area-inset-*)`, CSS Grid. moa/haru/kanade/baybayin 공통 토큰 세트 주석 명시 |
| **PWA성 메타** | `theme-color`, `apple-mobile-web-app-*`, `viewport-fit=cover` 등 모바일 웹앱 메타 포함 (매니페스트·서비스워커는 없음) |
| **저장소** | 브라우저 **localStorage** (아래 참조) |
| **브라우저 API** | `localStorage`, `matchMedia`, `navigator.language`, `Date` |

> 확인: `<head>`에 외부 스크립트/스타일/폰트 링크가 없고, 코드 전체에서 `fetch`·Firebase·CDN·MongoDB 참조가 발견되지 않았습니다. 100% 클라이언트 사이드 정적 페이지입니다.

---

## 🏗️ 시스템 구조

**단일 파일** `index.html` 하나에 마크업·스타일·로직이 모두 들어 있습니다. 부팅·렌더 흐름:

1. **테마 선적용 스크립트** (`<head>` 최상단) — 다른 리소스보다 먼저 `localStorage["hub-theme"]`(없으면 시스템 선호)를 읽어 `<html data-theme>`를 세팅 → 다크/라이트 깜빡임 방지.
2. **DOM 스켈레톤** — `.wrap` 안에 앱바, 히어로, 통계 그리드(`#stat-grid`), 4개 패널(조기전역/병가/연가·특별/복무현황), 캘린더, 사용 기록 목록, 하단에 설정 시트·기록 시트·토스트.
3. **메인 스크립트 부팅** — 마지막 IIFE `init()`가 오늘 날짜로 캘린더 뷰 초기화 후 `renderAll()` + `applyLang(L)` 호출.

### 상태 관리 & 라우팅
- **라우팅 없음** — 단일 화면. "설정"과 "사용 기록"은 라우트가 아니라 하단 시트(`.sheet-wrap`) 토글로 표시.
- **전역 상태 변수:** `cfg`(설정 객체), `log`(사용 기록 배열), 언어 `L`, 캘린더 뷰 `viewY`/`viewM`, 활성 병가 시나리오 `activeScenario`, 휴가구간 표시 `showLeave`, 기록 시트 상태 `logDate`/`logType`.
- **렌더 파이프라인:** `renderAll()`이 `renderStats → renderEarly → renderSick → renderCover → renderSvc → renderFilter → renderCalendar → renderLog`를 순서대로 호출. 설정 저장·기록 추가/삭제·언어 전환 시 재호출하여 화면 전체를 다시 그림(가상 DOM 없이 `innerHTML` 직접 갱신).

### 핵심 함수(계산 엔진)

| 함수 | 역할 |
|------|------|
| `isWeekend / isHol / isOff / isWork` | 요일·공휴일·근무일 판정(설정의 토/일 근무 토글 반영) |
| `workBack(end, n)` | `end`에서 거꾸로 세어 n번째 **근무일** 날짜 반환 |
| `workCount(a, bEx)` | 구간 `[a, bEx)` 안의 근무일 수 |
| `dday(d)` | 오늘 기준 D-day(일수) |
| `sum(type) / sickRemain / annRemain / spcRemain` | 로그 합산 후 병가(시간)·연가·특별휴가(일) 잔여 계산 |
| `blockInfo()` | 연가+특별휴가 전량을 소집해제 직전에 배치 → 첫 휴가일·마지막 근무일·날짜별 라벨(특별휴가 먼저·연가 뒤) 산출 |
| `scenarios() / scenarioSet(rate)` | 병가를 하루 3/4/5/8h로 소진하는 시나리오와 캘린더 표시용 날짜 집합 |
| `fmtH / fmtDays / fmtDate / dot` | 시간(→일+시간)·일수·날짜 표기 포맷 |

---

## 🗂️ 데이터

앱이 다루는 데이터는 **① 설정(cfg)**, **② 사용 기록(log)**, **③ 코드 상수(공휴일·복무현황·번역)** 로 나뉩니다.

### ① 설정 객체 `cfg` — 기본값 `DEF`

```js
const DEF = {
  start:"2025-01-16",      // 소집일(복무 시작)
  end:"2026-10-15",        // 소집해제 예정일
  departure:"2026-09-22",  // 출국일(조기전역 고정 기준)
  hpd:8,                   // 하루 복무시간(시간)
  sat:false, sun:false,    // 토/일 근무 여부
  annual:6,  sick:74,      // 연가 잔여(일), 병가 잔여(시간)
  special:9,               // 특별휴가 잔여(일)
  inclEnd:true             // 소집해제일도 휴가(연가)에 포함
};
```

### ② 사용 기록 배열 `log` — 항목 스키마

```js
{ id:"l...",         // 고유 id (시드는 "seed-...")
  date:"2026-06-04", // YYYY-MM-DD
  type:"sick",       // "sick" | "annual" | "special"
  hours:1 }          // 병가=시간 그대로, 연가·특별휴가=일수×hpd로 환산 저장
```

- **코드 시드(SEED_LOG):** 코드에 박아둔 기본 기록 1건(`2026-06-04` 병가 1시간). `loadLog()`가 localStorage 기록과 **id 기준 병합**(중복 제외)하므로 모든 기기에서 시드가 항상 보입니다.

```js
const SEED_LOG = [
  { id:"seed-20260604-sick1h", date:"2026-06-04", type:"sick", hours:1 },
];
```

### ③ 공휴일 상수 `HOL` — `YYYY-MM-DD → 이름`

대한민국 관공서 공휴일 + **대체공휴일** 매핑(2025~2026년, 37개 키). 근무일 계산과 캘린더 이름 표기에 사용. 예시:

```js
const HOL = {
  "2025-01-28":"설날", "2025-05-05":"어린이날·석탄", "2025-06-06":"현충일",
  "2026-02-16":"설날", "2026-05-24":"석가탄신", "2026-07-17":"제헌절",
  "2026-09-24":"추석", "2026-10-03":"개천절", "2026-12-25":"성탄절", /* … */
};
```

공휴일 이름은 `HOL_TR` 맵으로 한↔영 변환(예: `설날→Seollal`, `추석→Chuseok`, `현충일→Memorial Day`).

### ④ 복무 현황 상수 `SVC` — 표시 전용(근무상황관리 스크린샷 기준)

```js
const SVC = {
  name:"이연준", start:"2025-01-16", end:"2026-10-15", dutyDays:638,
  annualBase:28, annualUsed:22, y1:"15 / 15", y2:"7 / 13",
  sickBase:30, sickUsed:"20일 6시간",
  specialBase:20, specialUsed:11, gong:1,
  normal:257, military:16, jobEdu:12, baseEdu:5
};
```

> `SVC`는 계산에 쓰이지 않고 "복무 현황" 패널에 그대로 표시되는 참고 값입니다(실제 잔여 계산은 `cfg` + `log`로 수행).

---

## 💾 저장소 / DB

**Firebase·서버·계정 없음.** 모든 상태는 브라우저 **localStorage**에만 저장되며, 로그·설정은 이 브라우저에서만 유지됩니다.

### localStorage 키 표

| 키 | 용도 | 형식 |
|----|------|------|
| `hub-theme` | 라이트/다크 테마 (모아 계열 앱과 **공유**) | `"light"` \| `"dark"` |
| `dday-cfg` | 설정 객체 `cfg` (날짜·시간·잔여·근무토글) | JSON |
| `dday-log` | 사용 기록 배열 `log` | JSON 배열 |
| `dday-lang` | UI 언어 | `"ko"` \| `"en"` |

### 폴백 / 게스트 동작
- 저장·로드는 모두 `try/catch`로 감싸 localStorage 접근이 막혀도(프라이빗 모드 등) 오류 없이 진행. 이 경우 값은 세션 동안 메모리에서만 유지.
- 설정이 없으면 `DEF`로, 로그가 없어도 `SEED_LOG` 병합으로 최소 1건 시드가 표시됩니다.
- 언어 초기값: 저장값 → 없으면 `navigator.language`가 `ko`로 시작하면 한국어, 아니면 영어.
- "기본값으로 초기화" 버튼은 **설정만** `DEF`로 되돌리고 사용 기록은 유지합니다.

---

## 🌐 외부 API·의존성

**외부 API·SDK·키가 전혀 없습니다.** 네트워크 호출(fetch/XHR)도 없고, 유일한 외부 링크는 앱바·푸터의 **모아 허브 링크**(`clayborneyeounjunlee.github.io/moa/`)뿐입니다. Kakao/Google/환율/Web Speech 등 어떤 서드파티 서비스도 사용하지 않습니다. 따라서 API 키를 넣을 위치도, 필요도 없습니다.

---

## ▶️ 로컬 실행 방법

빌드·설치 과정이 없습니다. `index.html`을 브라우저로 열기만 하면 됩니다. `file://`로도 동작하지만, 로컬 정적 서버로 여는 편을 권장합니다.

```bash
# 저장소 클론
git clone https://github.com/ClayborneYeounjunLee/dday.git
cd dday

# 아무 정적 서버로 실행 (택 1)
python -m http.server 5173        # → http://localhost:5173
npx serve .                       # Node가 있으면
```

> `package.json`이 없으므로 `npm install` / npm 스크립트는 없습니다. 순수 정적 파일입니다.

---

## 🚀 배포

**GitHub Pages** 정적 호스팅입니다.

1. 저장소 → **Settings → Pages**.
2. **Source: Deploy from a branch**, 브랜치 `main` / 폴더 `/ (root)` 선택.
3. 저장하면 `https://<사용자명>.github.io/dday/`에 게시 (현재: https://clayborneyeounjunlee.github.io/dday/).
4. `index.html`을 수정·push 하면 자동 재배포. 빌드 단계 없음.

---

## 📁 파일 구조

```
dday/
├── index.html   # 앱 전체 — HTML 마크업 + <style> 디자인토큰/CSS + <script> 계산·렌더·i18n 로직
└── .git/        # git 메타데이터
```

> 소스 파일은 `index.html` **단 하나**입니다. 별도의 데이터 `.js`, `package.json`, `firebase.json`/`.firebaserc`, `.gitignore`는 없습니다.

`index.html` 내부 논리 구성:

| 영역 | 내용 |
|------|------|
| `<head>` 인라인 스크립트 | 테마 선적용(깜빡임 방지) |
| `<style>` | 디자인 토큰(라이트/다크), 레이아웃, 캘린더/시트/토스트 스타일 |
| `<body>` 마크업 | 앱바·히어로·통계 그리드·패널 4종·캘린더·기록 목록·설정/기록 시트 |
| 메인 `<script>` | 유틸/i18n(`T`,`tr`)/테마/`cfg`·`log`/공휴일`HOL`/날짜·잔여 계산/`render*`/이벤트 핸들러/`init()` |

---

## 🔗 관련 앱 (모아 허브 · 형제 앱)

이 앱은 **모아(moa) 허브** 계열 단일 파일 앱 중 하나로, 동일한 디자인 토큰 시스템(주석에 `moa/haru/kanade/baybayin` 공통 명시)과 `hub-theme` 테마 키를 공유합니다. 앱바의 `◈` 버튼은 모아 허브(https://clayborneyeounjunlee.github.io/moa/)로 직접 이동하고, 푸터의 "모아로 돌아가기" 링크는 상대 경로(`../index.html`)로 상위 페이지를 가리킵니다.

---

### 참고 · 계산 가정

- 병가는 **출국 전 마지막 근무일까지 조퇴로 소진**, 연가·특별휴가는 **출국일~소집해제일 구간을 채우는** 용도로 서로 분리해 계산합니다.
- 잔여는 "오늘 기준" 값이며, 사용 기록을 추가하면 잔여에서 자동 차감됩니다.
- 근무일 판정은 설정의 토/일 근무 토글과 `HOL` 공휴일 맵을 함께 반영합니다. 공휴일 데이터는 2025~2026년 범위로 하드코딩돼 있으니, 복무 기간이 이 범위를 벗어나면 코드의 `HOL` 상수를 갱신해야 합니다.

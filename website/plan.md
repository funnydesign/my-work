# D그라운드 웹사이트 — 작업 기록 & 구현 명세

## 프로젝트 개요

카카오모빌리티(kakaomobility.com) 디자인을 참고한 **D그라운드** 프로필 정적 웹사이트.  
빌드 도구 없음 — 브라우저에서 `index.html` 직접 실행.

---

## 파일 구조

```
website/
├── index.html       — 전체 HTML 마크업 + 인라인 JS
├── style.css        — 전체 스타일 (단일 파일)
├── plan.md          — 이 파일
└── image/
    ├── 카카오모빌리티_1.jpg    — GNB 기본 상태 레퍼런스
    ├── 카카오모빌리티_1_1.jpg  — GNB hover 상태 레퍼런스
    ├── 카카오모빌리티_2.jpg    — 스토리 섹션 전체 레퍼런스
    ├── 카카오모빌리티_2_1.jpg  — 1번 스토리 실제 사용 이미지 (항공 고속도로)
    └── 카카오모빌리티_2_2.jpg  — 스토리 섹션 추가 레퍼런스
```

---

## 디자인 시스템

### CSS 변수
```css
:root {
  --yellow:     #FFCD00;   /* 포인트 컬러 */
  --black:      #1A1A1A;   /* 주요 텍스트 */
  --gray-dark:  #333;
  --gray-mid:   #767676;
  --gray-light: #F7F7F7;
  --white:      #fff;
  --max-w:      1200px;    /* 콘텐츠 최대 너비 */
}
```

### 폰트
```css
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&display=swap');
font-family: 'Noto Sans KR', 'Apple SD Gothic Neo', sans-serif;
```

---

## 구현된 섹션 목록

| 순서 | 섹션 클래스   | id         | 내용                          |
|------|--------------|------------|-------------------------------|
| 1    | `.gnb`        | `gnb`      | 상단 네비게이션 (fixed)          |
| 2    | `.hero`       | `hero`     | 풀스크린 히어로 + 카운터           |
| 3    | `.stories`    | `stories`  | 3개 행 텍스트+이미지 교차          |
| 4    | `.services`   | `services` | 서비스 아이콘 그리드 (다크 배경)     |
| 5    | `.newsroom`   | `newsroom` | 뉴스 카드 3열                    |
| 6    | `.recruit`    | `recruit`  | 채용 섹션 (다크 배경)              |
| 7    | `.footer`     | —          | 다단 링크 + 카피라이트             |

---

## 섹션별 구현 상세

### 1. GNB (상단 네비게이션)

**HTML 구조**
```html
<header class="gnb" id="gnb">
  <div class="gnb-inner">
    <a href="#" class="gnb-logo">D그라운드</a>
    <nav class="gnb-nav">
      <a href="#hero"     class="gnb-nav__item" data-section="hero">D그라운드</a>
      <a href="#stories"  class="gnb-nav__item" data-section="stories">기술과 안전</a>
      <a href="#services" class="gnb-nav__item" data-section="services">서비스</a>
      <a href="#"         class="gnb-nav__item">동행과 상생</a>
      <a href="#newsroom" class="gnb-nav__item" data-section="newsroom">뉴스룸</a>
      <a href="#"         class="gnb-nav__item">안내</a>
    </nav>
    <a href="#recruit" class="gnb-recruit">인재 영입 ↗</a>
    <button class="gnb-hamburger" id="hamburger" aria-label="메뉴 열기">
      <span></span><span></span><span></span>
    </button>
  </div>
</header>

<!-- 모바일 전용 드롭다운 메뉴 -->
<div class="gnb-mobile-menu" id="mobileMenu">
  <nav class="mobile-nav">
    <a href="#hero"    class="mobile-nav__item">D그라운드</a>
    <a href="#stories" class="mobile-nav__item">기술과 안전</a>
    <!-- ... -->
    <a href="#recruit" class="mobile-nav__item mobile-nav__recruit">인재 영입 ↗</a>
  </nav>
</div>
```

**CSS 핵심 값**
```css
.gnb-inner { padding: 0 5rem; height: 80px; }
/* height 변경 시: .gnb-nav__item height, .gnb-mobile-menu top 도 동일하게 변경 */
.gnb-nav__item { padding: 0 1.5rem; height: 80px; font-size: 0.85rem; }
```

**동작 방식**
- 기본: 투명 배경 + 흰 텍스트
- hover / 스크롤: 흰 배경 + 검정 텍스트 (`.gnb:hover`, `.gnb.scrolled`)
- hover 밑줄: `::after` pseudo-element `scaleX(0 → 1)` 트랜지션
- 현재 섹션 밑줄: `IntersectionObserver`가 `[data-section]` 매핑으로 `.active` 토글
- 모바일(≤768px): nav/recruit 숨김, 햄버거 버튼 표시, 드롭다운 슬라이드

---

### 2. Hero 섹션

**내용**
- 풀스크린(`height: 100vh`) 다크 그라데이션 배경
- `we move life` 아이웨이브 + 메인 타이틀
- 누적 이동거리 카운터 (`98,347,261 km`, 700ms 딜레이 후 80스텝 카운트업)
- 하단 SCROLL 힌트 (선형 애니메이션)

**배경 장식**
```css
/* 우상단 노란 glow */
.hero-bg::before { width:700px; height:700px; border-radius:50%; background: radial-gradient(circle, rgba(255,205,0,0.18) ...); top:-100px; right:-100px; }
/* 좌하단 보조 glow */
.hero-bg::after  { width:400px; height:400px; bottom:80px; left:60px; }
```

**카운터 JS**
```js
el.textContent = (target - 500000).toLocaleString('ko-KR'); // 초기값 표시
setTimeout(() => {
  let current = target - 500000;
  const step = Math.ceil(500000 / 80);
  const timer = setInterval(() => {
    current = Math.min(current + step, target);
    el.textContent = current.toLocaleString('ko-KR');
    if (current >= target) clearInterval(timer);
  }, 20);
}, 700);
```

---

### 3. Stories 섹션

**레이아웃 규칙**
```css
.story-row { display: grid; grid-template-columns: 1fr 1fr; align-items: center; }
/* 2번 행 이미지 좌측으로 → direction:rtl 금지, order:-1 사용 */
.story-row--reverse .story-row__img { order: -1; }

/* 중앙 방향 padding=0 으로 이미지가 화면 중앙에서 시작하는 효과 */
.story-row__img          { padding: 60px 8vw 60px 0; }   /* 1·3번: 이미지 우측 */
.story-row--reverse .story-row__img { padding: 60px 0 60px 8vw; } /* 2번: 이미지 좌측 */
.story-img-box { width:100%; aspect-ratio: 16/9; }        /* 높이 일관성 */
```

**1번 스토리** — `<img src="image/카카오모빌리티_2_1.jpg">` 실제 이미지  
**2번 스토리** — SVG 데이터 시각화 (노드 네트워크, 파란색 pulsing 애니메이션)
```css
.story-img-box--2 { background: linear-gradient(135deg, #0d1b2a, #1a2a4a, #0f2040); }
/* 노드 애니메이션 */
.dv-node { animation: nodePulse 2.8s ease-in-out infinite; transform-box: fill-box; }
@keyframes nodePulse { 0%,100% { opacity:.45; transform:scale(1); } 50% { opacity:1; transform:scale(1.5); } }
```

**3번 스토리** — 노란 그라데이션 배경 + SVG 아이콘 3개 (자동차/방패/사람)
```css
.story-img-box--3 { background: linear-gradient(135deg, #FFF9E6, #FFF3CC, #FFE880); }
```

---

### 4. Services 섹션

**특징**
- 다크 배경(`var(--black)`)
- 상단 대각선 컷: `clip-path: polygon(0 3rem, 100% 0, 100% 100%, 0 100%)`
- 9개 서비스 아이템 → 라인 SVG 아이콘 (stroke 기반, 이모지 → SVG 교체)

**SVG 아이콘 목록**
| 서비스 | SVG 내용 |
|--------|----------|
| 택시   | 차체 + 바퀴 2개 + 수평선 |
| 대리   | 스티어링 휠 (원 + 3선) |
| 바이크 | 바퀴 2개 + 차체 경로 |
| 주차   | 사각형 + P 글자 경로 |
| 내비   | 삼각 항법 포인터 |
| 시외버스 | 사각형 차체 + 바퀴 2개 |
| 기차   | 사각형 + 바퀴 + 레일 |
| 항공   | 비행기 실루엣 |
| 퀵/택배 | 오픈 박스 |

**hover 효과**
```css
.service-item:hover .service-icon { color: var(--yellow); transform: translateY(-3px); }
.service-item:hover span { color: var(--white); }
```

**그리드 반응형**
```css
/* ≥1150px */ grid-template-columns: repeat(9, 1fr);
/* ≤1150px */ grid-template-columns: repeat(5, 1fr);
/* ≤768px  */ grid-template-columns: repeat(3, 1fr);
```

---

### 5. Newsroom 섹션

**카드 구조**
```html
<article class="news-card">
  <div class="news-thumb news-thumb--1">
    <div class="news-thumb-overlay">
      <span class="news-thumb-bg-text">NEWS</span>  <!-- 대형 반투명 배경 텍스트 -->
      <svg><!-- 문서 아이콘 --></svg>
    </div>
  </div>
  <div class="news-body">
    <span class="news-tag">뉴스</span>
    <h3 class="news-title">...</h3>
    <p class="news-date">2026.06.26</p>
  </div>
</article>
```

**썸네일 배경 그라데이션**
```css
.news-thumb--1 { background: linear-gradient(135deg, #667eea, #764ba2); }
.news-thumb--2 { background: linear-gradient(135deg, #f093fb, #f5576c); }
.news-thumb--3 { background: linear-gradient(135deg, #4facfe, #00f2fe); }
```

**배경 텍스트**: NEWS / REPORT / STORY — `font-size: 3.8rem; color: rgba(255,255,255,0.1); bottom:-0.15em; right:0.4rem`

**hover 효과**: 카드 `-6px` 위로 이동 + 그림자, 아이콘 scale(1.12)

---

### 6. Recruit 섹션

**배경 레이어 (z-index 순서)**
1. `::before` — 중앙 노란 radial glow (원형 800×800)
2. `::after` — 노란 도트 그리드 패턴 (`background-size: 38px 38px`)
3. `.recruit-inner` — 실제 콘텐츠 (`z-index: 1`)

**버튼**: 노란 배경 필 버튼 (`border-radius: 999px`)

---

### 7. Footer

**구조**: 8열 그리드 (D그라운드, 기술과 안전, 서비스, 동행과 상생, 뉴스룸, 안내, Follow us, 법률정보)  
반응형: ≤1024px → 4열, ≤768px → 2열

---

## JS 동작 전체 목록 (index.html 하단 `<script>`)

```js
// 1. GNB 스크롤 배경 전환
window.addEventListener('scroll', () => {
  gnb.classList.toggle('scrolled', window.scrollY > 10);
});

// 2. 햄버거 메뉴 토글 (모바일)
hamburger.addEventListener('click', () => {
  hamburger.classList.toggle('open');
  mobileMenu.classList.toggle('open');
});

// 3. 모바일 메뉴 링크 클릭 시 자동 닫기
mobileMenu.querySelectorAll('a').forEach(link => {
  link.addEventListener('click', () => { ... });
});

// 4. 누적 이동거리 카운터 (700ms 딜레이)
setTimeout(() => { setInterval(..., 20); }, 700);

// 5. 스크롤 진입 fade-up 애니메이션 (IntersectionObserver)
// - 서비스 아이템: 0.05s 간격 스태거
// - 뉴스 카드: 0.1s 간격 스태거
const fadeObserver = new IntersectionObserver(..., { threshold: 0.12 });
document.querySelectorAll('.fade-up').forEach(el => fadeObserver.observe(el));

// 6. GNB 현재 섹션 하이라이트
const sectionObserver = new IntersectionObserver(..., { threshold: 0.4 });
document.querySelectorAll('section[id]').forEach(s => sectionObserver.observe(s));
```

---

## 애니메이션 시스템

### fade-up (스크롤 진입)
```css
.fade-up { opacity: 0; transform: translateY(36px); transition: opacity 0.65s ease, transform 0.65s ease; }
.fade-up.visible { opacity: 1; transform: translateY(0); }
```

**적용 대상**: `.story-row`, `.service-item`, `.news-card`, `.section-eyebrow`, `.section-title`, `.recruit-inner`

### nodePulse (SVG 데이터 시각화)
```css
@keyframes nodePulse { 0%,100% { opacity:.45; transform:scale(1); } 50% { opacity:1; transform:scale(1.5); } }
```

### scrollDown (HERO 스크롤 힌트)
```css
@keyframes scrollDown { 0% { transform:scaleY(0); transform-origin:top; } 50% { transform:scaleY(1); transform-origin:top; } 51% { transform-origin:bottom; } 100% { transform:scaleY(0); transform-origin:bottom; } }
```

---

## 반응형 브레이크포인트

| 너비     | 주요 변경 사항                                                |
|----------|--------------------------------------------------------------|
| ≤1200px  | GNB padding 5rem → 3rem, 메뉴 간격 1.5rem → 1rem             |
| ≤1150px  | 서비스 그리드 9열 → 5열                                       |
| ≤1024px  | 서비스 5열, 푸터 8열 → 4열, GNB padding 2rem                  |
| ≤768px   | GNB 햄버거 전환, 스토리 1열, 뉴스 1열, 서비스 3열, 푸터 2열    |

**Services clip-path 반응형**
```css
/* 데스크탑 */ clip-path: polygon(0 3rem, 100% 0, 100% 100%, 0 100%); padding-top: calc(8rem + 3rem);
/* 모바일  */ clip-path: polygon(0 1.5rem, 100% 0, 100% 100%, 0 100%); padding-top: calc(5rem + 1.5rem);
```

---

## 알려진 주의사항

| 문제 | 원인 | 해결책 |
|------|------|--------|
| `background-size: cover` 미작동 | headless Chrome 한계 | `<img>` + `object-fit: cover` |
| 행 반전 레이아웃 깨짐 | `direction: rtl` | CSS `order: -1` |
| `height: 100%` 자식이 0 | 부모 height 미지정 | 부모에 고정 height 설정 |
| GNB 높이 변경 후 밑줄 위치 이상 | `::after bottom` 기준 | `bottom: 0` 유지, height만 변경 |
| 스토리 이미지 높이 불일치 | `<img>` vs gradient box | `aspect-ratio: 16/9` 통일 |
| `transform-box: fill-box` 없으면 SVG 노드 중심 이상 | SVG transform-origin 기본값 | `transform-box: fill-box` 추가 |

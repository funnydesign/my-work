# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

**D그라운드** 프로필 웹사이트.  
카카오모빌리티(kakaomobility.com) 디자인을 참고하여 제작한 순수 정적 HTML/CSS/JS 사이트.  
빌드 도구 없음 — 브라우저에서 `index.html`을 직접 열면 바로 실행됨.

---

## 파일 구성

```
website/
├── index.html              — 메인 HTML (전체 레이아웃)
├── style.css               — 전체 스타일 (단일 파일)
└── image/
    ├── 카카오모빌리티_1.jpg    — GNB 기본 상태 레퍼런스 (투명 배경, 흰 텍스트)
    ├── 카카오모빌리티_1_1.jpg  — GNB hover 상태 레퍼런스 (흰 배경, 검정 텍스트)
    ├── 카카오모빌리티_2.jpg    — 스토리 섹션 전체 스크린샷 레퍼런스
    ├── 카카오모빌리티_2_1.jpg  — 1번 스토리에 사용하는 항공 고속도로 사진
    └── 카카오모빌리티_2_2.jpg  — 스토리 섹션 레퍼런스 추가
```

---

## 디자인 시스템

### CSS 변수 (`:root`)
```css
--yellow:     #FFCD00   /* 포인트 컬러 */
--black:      #1A1A1A   /* 주요 텍스트 */
--gray-dark:  #333
--gray-mid:   #767676
--gray-light: #F7F7F7
--white:      #fff
--max-w:      1200px    /* 콘텐츠 최대 너비 */
```

### 폰트
```css
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&display=swap');
font-family: 'Noto Sans KR', 'Apple SD Gothic Neo', sans-serif;
```

---

## 섹션 구조 (index.html)

| 순서 | 클래스 | 설명 |
|------|--------|------|
| 1 | `.gnb` | 상단 네비게이션 (fixed) |
| 2 | `.hero` | 풀스크린 다크 히어로 + 카운터 |
| 3 | `.stories` | 3개 행 텍스트+이미지 교차 배치 |
| 4 | `.services` | 서비스 아이콘 그리드 (다크 배경) |
| 5 | `.newsroom` | 뉴스 카드 3열 |
| 6 | `.recruit` | 채용 섹션 (다크 배경) |
| 7 | `.footer` | 다단 링크 + 카피라이트 |

---

## GNB (상단 네비게이션) 상세

### 구조
```html
<header class="gnb" id="gnb">
  <div class="gnb-inner">
    <a href="#" class="gnb-logo">D그라운드</a>
    <nav class="gnb-nav">
      <a href="#" class="gnb-nav__item">D그라운드</a>
      <a href="#" class="gnb-nav__item">기술과 안전</a>
      <a href="#" class="gnb-nav__item">서비스</a>
      <a href="#" class="gnb-nav__item">동행과 상생</a>
      <a href="#" class="gnb-nav__item">뉴스룸</a>
      <a href="#" class="gnb-nav__item">안내</a>
    </nav>
    <a href="#" class="gnb-recruit">인재 영입 ↗</a>
  </div>
</header>
```

### 핵심 CSS 값
```css
.gnb-inner {
  padding: 0 5rem;   /* 좌우 여백 — 레퍼런스 기준 */
  height: 80px;      /* 상하 높이 — 카카오모빌리티 레퍼런스 기준 (64px→80px 조정) */
}
.gnb-nav__item {
  padding: 0 1.5rem; /* 메뉴 항목 좌우 간격 */
  height: 80px;      /* gnb-inner height와 반드시 동일해야 함 */
  font-size: 0.85rem;
}
```

### 동작 방식
- **기본 상태**: 투명 배경 + 흰색 텍스트 (히어로 이미지 위에 오버레이)
- **hover 시**: `.gnb:hover` → 흰 배경 + 검정 텍스트 (CSS만으로 처리, JS 불필요)
- **스크롤 시**: JS가 `.scrolled` 클래스 토글 → 흰 배경 + 검정 텍스트
- **hover 밑줄**: `::after` pseudo-element + `scaleX(0→1)` 트랜지션
- **메뉴 중앙 정렬**: `.gnb-nav { flex: 1; justify-content: center; }` — 로고와 인재영입 버튼 사이에서 중앙 정렬
- **모바일(768px 이하)**: `.gnb-nav`, `.gnb-recruit` 숨기고 햄버거 버튼 표시

### GNB 높이 변경 시 주의
`.gnb-inner`와 `.gnb-nav__item`의 `height`를 **반드시 동일하게** 맞출 것.  
`.gnb-mobile-menu { top: 80px }` 도 함께 변경해야 함.

---

## Stories 섹션 레이아웃

### 구조
- 3개의 `article.story-row` 행
- 1번·3번: 텍스트 좌 / 이미지 우
- 2번(`.story-row--reverse`): 이미지 좌 / 텍스트 우

### 이미지 반전 방법
```css
/* direction: rtl 사용 금지 — 패딩/텍스트 방향 깨짐 발생 */
.story-row--reverse .story-row__img { order: -1; }
```

### center 기준 이미지 정렬
이미지가 화면 중앙에서 시작하는 것처럼 보이게 하려면 중앙 방향 padding을 0으로 설정:
```css
/* 1·3번: 이미지가 오른쪽 → 이미지 왼쪽(center 방향) padding = 0 */
.story-row__img { padding: 60px 8vw 60px 0; }

/* 2번: 이미지가 왼쪽 → 이미지 오른쪽(center 방향) padding = 0 */
.story-row--reverse .story-row__img { padding: 60px 0 60px 8vw; }
```

### 이미지 높이 일관성
```css
/* fixed height 대신 aspect-ratio 사용 — 이미지 크기에 무관하게 일관된 비율 유지 */
.story-img-box { width: 100%; aspect-ratio: 16 / 9; }
```

### 실제 이미지 (`<img>` 태그)
```css
.story-row__img img { width: 100%; height: auto; object-fit: cover; }
```

---

## JS 동작 (index.html 하단 `<script>`)

```js
// 1. 스크롤 감지 → GNB 배경 전환
window.addEventListener('scroll', () => {
  gnb.classList.toggle('scrolled', window.scrollY > 10);
});

// 2. 누적 이동거리 카운터 애니메이션
// target에서 500,000 아래부터 시작해서 80 스텝으로 카운트업
const target = 98347261;
let current = target - 500000;
const step = Math.ceil(500000 / 80);
const timer = setInterval(() => {
  current = Math.min(current + step, target);
  el.textContent = current.toLocaleString('ko-KR');
  if (current >= target) clearInterval(timer);
}, 20);
```

---

## 반응형 브레이크포인트

| 너비 | 변경 내용 |
|------|-----------|
| ≤1200px | GNB 좌우 padding 5rem → 3rem, 메뉴 간격 1.5rem → 1rem |
| ≤1150px | 서비스 그리드 9열 → 5열 |
| ≤1024px | 서비스 5열, 푸터 8열 → 4열, GNB padding 2rem |
| ≤768px | GNB 햄버거 메뉴 전환, 스토리 1열, 뉴스 1열, 서비스 3열, 푸터 2열 |

---

## 알려진 주의사항 및 트릭

| 문제 | 원인 | 해결책 |
|------|------|--------|
| `background-size: cover` 미작동 | headless Chrome 한계 | `<img>` + `object-fit: cover` 사용 |
| 행 반전 시 레이아웃 깨짐 | `direction: rtl` 사용 | CSS `order: -1` 사용 |
| `height: 100%` 자식이 0으로 렌더링 | 부모에 명시적 height 없음 | 부모에 고정 height 설정 |
| GNB 높이 조정 후 밑줄 위치 어긋남 | `::after` bottom 기준 | `bottom: 0` 유지, height만 변경 |
| 스토리 섹션 높이 불일치 | `<img>`와 gradient box 높이 차이 | `aspect-ratio: 16/9` 통일 |

---

## 향후 작업 시 참고

- 레퍼런스 이미지는 `image/` 폴더의 `카카오모빌리티_*.jpg` 파일 참고
- 새 섹션 추가 시 `--max-w: 1200px` 기준으로 `max-width` + `margin: 0 auto` 패턴 유지
- 색상은 반드시 CSS 변수 사용 (`--yellow`, `--black` 등)
- 애니메이션은 `.fade-up` + IntersectionObserver 패턴 또는 CSS transition 활용

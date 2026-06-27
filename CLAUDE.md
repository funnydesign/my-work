# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

**D그라운드** 프로필 웹사이트 — 카카오모빌리티(kakaomobility.com) 스타일을 참고하여 제작한 정적 HTML/CSS/JS 사이트.

빌드 도구 없음. 브라우저에서 `website/index.html`을 직접 열면 됨.

## 파일 구성

```
my-work/
├── CLAUDE.md
├── memo.txt
├── image_1.png
└── website/
    ├── index.html       — 메인 HTML
    ├── style.css        — 전체 스타일
    └── image/
        ├── 카카오모빌리티_1.jpg    — 네비게이션 기본 상태 레퍼런스 (투명 배경, 흰 텍스트)
        ├── 카카오모빌리티_1_1.jpg  — 네비게이션 hover 상태 레퍼런스 (흰 배경, 검정 텍스트)
        ├── 카카오모빌리티_2.jpg    — 스토리 섹션 레퍼런스 (전체 페이지 스크린샷)
        ├── 카카오모빌리티_2_1.jpg  — 항공 고속도로 사진 (1번 스토리에 사용)
        └── 카카오모빌리티_2_2.jpg  — 스토리 섹션 레퍼런스 추가
```

## 디자인 시스템

### CSS 변수
```css
--yellow: #FFCD00
--black:  #1A1A1A
--white:  #FFFFFF
```

### 폰트
Noto Sans KR (Google Fonts)

## 주요 섹션 구조

1. **GNB (상단 네비게이션)** — `position: fixed`, 스크롤/hover 시 흰 배경 전환
2. **Hero** — 전체 화면 다크 배경, 카운터 애니메이션
3. **Stories** — 3개 행, 텍스트+이미지 교차 배치
4. **Services** — 서비스 아이콘 그리드
5. **Newsroom** — 뉴스 카드 3열
6. **Recruit** — 다크 배경 채용 섹션
7. **Footer** — 다단 링크 + 카피라이트

## 네비게이션 (GNB) 구현 상세

```css
.gnb-inner {
  padding: 0 5rem;   /* 좌우 여백 */
  height: 80px;      /* 상하 높이 — 카카오모빌리티 레퍼런스 기준 */
}
.gnb-nav__item {
  padding: 0 1.5rem; /* 메뉴 항목 간격 */
  height: 80px;      /* gnb-inner와 반드시 동일해야 함 */
  font-size: 0.85rem;
}
```

- 기본 상태: 투명 배경 + 흰색 텍스트
- hover/스크롤 시: 흰 배경 + 검정 텍스트 (`.gnb:hover`, `.gnb.scrolled`)
- hover 밑줄: `::after` pseudo-element로 구현 (`scaleX` 트랜지션)
- 스크롤 감지: JS `window.addEventListener('scroll', ...)` → `.scrolled` 클래스 토글
- 메뉴 중앙 정렬: `.gnb-nav { flex: 1; justify-content: center; }`

## Stories 섹션 레이아웃

```css
.story-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
/* 2번 행 이미지를 왼쪽으로: order: -1 사용 (direction: rtl은 레이아웃 깨짐으로 사용 금지) */
.story-row--reverse .story-row__img { order: -1; }
```

- 이미지 정렬 기준: center 기준으로 1·3번 이미지는 왼쪽, 2번 이미지는 오른쪽
- 중앙 여백 제거 방식: 이미지쪽 center 방향 padding을 0으로 설정
  - 1·3번(이미지 우측): `padding: 60px 8vw 60px 0`
  - 2번(이미지 좌측): `padding: 60px 0 60px 8vw`
- 이미지 높이 일관성: `aspect-ratio: 16/9` 사용 (fixed height 대신)
- 실제 이미지(`<img>`)는 `object-fit: cover`로 컨테이너 채움

## 알려진 주의사항

- **headless Chrome에서 `background-size: cover` 미작동**: 배경 이미지가 렌더링 안 될 수 있음. `<img>` + `object-fit: cover` 방식으로 대체.
- **`direction: rtl`로 행 반전 시도 금지**: 패딩/방향 깨짐 발생. CSS `order: -1` 사용.
- **부모에 명시적 height 없으면 `height: 100%` 자식 미작동**: 절대 위치 자식에 `height: 100%` 필요 시 부모에 고정 height 필수.
- **GNB 높이 변경 시**: `.gnb-inner`와 `.gnb-nav__item`의 height를 반드시 동일하게 맞출 것.

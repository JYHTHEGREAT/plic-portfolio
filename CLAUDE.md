# Portfolio Sites — 코딩 표준 & 디자인 시스템

## 프로젝트 개요
- 5개 정적 포트폴리오 사이트 (단일 HTML 파일 아키텍처)
- 용도: 2월 25일 발표평가 시연 — 안정성과 시각적 완성도 우선

## 구조
```
portfolio/
├── CLAUDE.md             ← 이 파일
├── plicstudio/
│   └── index.html        ← 에이전시 포트폴리오 (다크, Apple 감성)
├── hospital/
│   └── index.html        ← 연세숲의원 (클린, 자연 컨셉)
├── academy/
│   └── index.html        ← 하이엘리영어학원 (모던, 활기찬)
├── pilates/
│   └── index.html        ← 캘리필라테스 (시네마틱, 프리미엄 웰니스)
└── dental/
    └── index.html        ← 미래플러스치과 (클린, 프리미엄 심미)
```

## 코딩 표준 (Vercel Best Practices 기반)

### 퍼포먼스
- **Layout Thrashing 금지**: DOM read/write 배치 분리, 인라인 스타일 조작 최소화
- **GPU 가속 애니메이션만 사용**: transform, opacity만 애니메이트 (width, height, top, left 금지)
- **Passive Event Listeners**: scroll/touch 이벤트에 `{ passive: true }` 필수
- **content-visibility: auto**: 뷰포트 밖 섹션에 적용하여 초기 렌더링 최적화
- **비핵심 리소스 지연 로딩**: 폰트 CDN은 `display=swap`, 애니메이션은 뷰포트 진입 시 시작

### JavaScript
- **모듈 스코프에 상수 호이스팅**: RegExp, 설정 객체, 반복 사용 값은 전역 스코프에 정의
- **IntersectionObserver 단일 인스턴스**: 모든 data-reveal 요소를 하나의 Observer로 관찰
- **Early Exit 패턴**: 불필요한 연산 조기 종료
- **함수 결과 캐싱**: 반복 호출 함수는 Map 캐시 활용
- **이벤트 리스너 중복 방지**: 전역 단일 리스너 → 위임 패턴

### CSS
- **CSS Custom Properties 디자인 토큰 체계**: 모든 색상, 간격, 타이포그래피를 :root 변수로 관리
- **clamp() 반응형 타이포그래피**: 미디어 쿼리 대신 fluid type scale
- **모바일 퍼스트 접근**: 기본 스타일은 모바일, min-width 미디어 쿼리로 확장
- **인라인 스타일 최소화**: `<style>` 블록에 클래스 기반 스타일링

### HTML
- **시맨틱 마크업**: nav, section, article, header, footer, main 적절히 사용
- **접근성**: aria-label, alt 속성, 키보드 포커스 관리, 충분한 색상 대비
- **SVG 최적화**: 소수점 최소화, 불필요한 속성 제거

## 사이트별 디자인 시스템

### plicstudio (다크 Apple 감성)
- 배경: #0a0a0a / 카드: #111111 / 보더: rgba(255,255,255,0.08)
- 텍스트: #f5f5f7 (주) / #86868b (부) / #8e8e93 (보조, WCAG AA 충족)
- 액센트: #2997ff (Apple Blue)
- 폰트: Pretendard Variable
- 커브: 16px (카드), 100px (버튼 필)
- 접근성: `:focus-visible` 글로벌 포커스 스타일 (accent 2px outline)
- 리소스 힌트: `<link rel="preconnect">` jsdelivr, googleapis, gstatic
- **Hero: Crystallizing Text + Breathing Glow (JS-only, Zero CSS dependency)**
  - 텍스트 결정화: 기하학 문자 스크램블 → 순차 해독 (STAGGER 70ms, 400ms/글자)
  - 숨쉬는 글로우: `heroGlowBreathe` 7초 scale 맥동 (1.0→1.05→1.0)
  - 도트 그리드: CSS radial-gradient 32px, opacity 0.04, 마우스 패럴랙스 ±3px
  - 터미널 커서: accent 2px 라인, 4회 깜빡 후 페이드아웃
  - 서브타이틀/CTA: 순차 fade+translateY 캐스케이드 진입
  - 시머: background-clip:text 그라데이션 스윕 (1회)
  - `prefers-reduced-motion` 시 transition만 none, 스크램블은 항상 실행 (데모 우선)
- **Works: Apple-style Tab Gallery + Slide Navigation (v6 — iframe live preview)**
  - Apple MacBook Pro "Get the highlights" 패턴 참조
  - 3개 업종 카테고리 탭 (순서): 필라테스·피트니스 (기본 활성) / 학원 / 병·의원
  - 필라테스·피트니스 탭: 1개 슬라이드 (캘리필라테스 browser-mock)
  - 학원 탭: 1개 슬라이드 (하이엘리영어학원 browser-mock)
  - 병·의원 탭: 2개 슬라이드 (연세숲의원 browser-mock + 미래플러스치과 browser-mock)
  - **전체 browser-mock 통일** (phone-mock 미사용, 데스크톱 뷰 통일)
  - **iframe 라이브 프리뷰**: 실제 포트폴리오 사이트 히어로를 iframe으로 표시
    - browser-mock: width 200% + scale(0.5) = ~1560px desktop viewport
    - `pointer-events: none` — 클릭이 nav zones으로 패스스루
    - `data-src` lazy loading — JS `loadSlideIframe()`가 활성 슬라이드/탭 전환 시 src 전환
  - 패널 내 슬라이드 내비게이션: `.works__slides` > `.works__slide` 구조
  - slide 2개 이상: 좌우 nav zones (opacity 0.35 → hover 0.85) + 도트 인디케이터 자동 활성화
  - slide 1개: nav/dots 숨김 (CSS `[data-slide-count="1"]` 셀렉터)
  - 도트 JS 동적 생성 — 확장성 확보 (HTML에 `.works__slide` 추가만 하면 됨)
  - 접근성: role="tablist/tab/tabpanel", roving tabindex, aria-selected, ArrowLeft/Right
  - easing 전체 `var(--ease)` 토큰 통일
  - 드래그/오토플레이 없음 — 수동 탭/슬라이드 전환만

### hospital (포레스트 그린)
- 배경: #FFFFFF / 보조배경: #F0FFF4
- 주색: #2D6A4F / 보조: #40916C / 연한: #74C69D / #D8F3DC
- 텍스트: #1A1A2E (주) / #6B7280 (부)
- 폰트: Noto Sans KR
- 커브: 16px (카드), 12px (버튼)

### academy (블루+코랄)
- 배경: #FFFFFF / 보조배경: #F8FAFC
- 주색: #2563EB / 코랄: #F97316 / 퍼플: #8B5CF6
- 텍스트: #1E293B (주) / #64748B (부)
- 폰트: Nunito (영문) + Noto Sans KR (한국어)
- 커브: 20px (카드), 24px (CTA 필)

### pilates (로즈골드 프리미엄)
- 배경: #FAF8F5 (웜 화이트) / 카드: #FFFFFF / 다크 섹션: #1A1612
- 주색: #C8A97E (로즈골드/샴페인) / 보조: #8B7355 (웜브라운)
- 텍스트: #2C2420 (주) / #8C8279 (부)
- 폰트: Cormorant Garamond (영문 헤딩) + Noto Sans KR (한국어 본문)
- 커브: 12px (카드), 100px (CTA 필)
- 핵심: CSS-only 시네마틱 히어로 애니메이션 (3개 씬 순환, 12-15초 루프)

### dental (프리미엄 심미치과)
- 배경: #FFFFFF / 보조: #F5F8FC
- 주색: #1B3A5C (네이비) / 액센트: #C9A84C (골드)
- 텍스트: #1A2332 (주) / #6B7D93 (부)
- 폰트: Noto Sans KR
- 커브: 16px (카드), 12px (버튼)

## 공통 패턴
- **스크롤 애니메이션**: IntersectionObserver + data-reveal 속성 시스템
- **네비게이션**: 고정 상단 + 스크롤 시 backdrop-filter 전환
- **반응형**: 768px (태블릿), 1024px (데스크톱) 브레이크포인트
- **모바일 메뉴**: checkbox 트릭 기반 CSS-only 토글
- **탭 갤러리 (plicstudio)**: 업종 카테고리 탭 + 패널 내 슬라이드 + iframe 라이브 프리뷰

## plicstudio QA 적용 이력 (2026-02-24)
- `:focus-visible` 글로벌 포커스 스타일 추가
- `--text-tertiary` #6e6e73 → #8e8e93 (WCAG AA 4.5:1 충족)
- `querySelector('#')` 에러 가드 추가
- 죽은 CSS ~80줄 삭제 (.work-item, .phone-mock 잔존 코드)
- 섹션 header margin 64px 통일
- Works easing `var(--ease)` 토큰 통일
- 화살표 초기 어포던스 opacity 0.35
- 탭 터치 타겟 44px+ (padding 12px)
- section label→heading 간격 24px 분리
- preconnect 3개 CDN 추가
- 이벤트 리스너 통합 (패널당 1개)
- roving tabindex 패턴 적용

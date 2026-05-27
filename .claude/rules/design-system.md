# 대시보드 디자인 시스템

`@dashboard` 에이전트와 모든 시각화에 공통 적용됩니다.
상세 컴포넌트 스펙은 `.claude/skills/dashboard-design/SKILL.md` 참고.

## 색상
- 외부 배경: `#000000`
- 내부 카드: `rgba(14, 12, 8, 0.97)` (웜 오프화이트)
- KPI 수치: `#0a0a0a` 검정 고정 — **수치에 색상 절대 금지**
- 좋음: `#35c995` / 나쁨: `#ef7d86` / 경고: `#efb05d`

## 차트
- 순수 SVG/Canvas만 사용 — CDN 라이브러리(Chart.js, D3 등) 금지

## 차트 카드 크기 규칙
- `.chart-card` 는 `display:flex; flex-direction:column` 으로 설정
- `.dash-charts:not(.full) .chart-card` → `min-height: 240px` (2열/3열 차트 행 높이 통일)
- `.dash-charts.full .chart-card` → `min-height: 200px`
- SVG는 `width:100%; height:auto; max-height:260px` 고정
- 풀너비 차트 SVG는 `max-height:300px` — viewBox 비율 때문에 높이가 폭발하는 것을 방지
- `.dash-charts` 는 `align-items:stretch` — 같은 행 카드 높이 자동 통일

## 레이아웃 비대칭 원칙
- 메인 차트 행은 `6:4` 또는 `7:3` 비율 권장 — `grid-template-columns: 1.5fr 1fr` 사용
- 모든 카드가 동등한 너비인 레이아웃 지양 (AI스럽게 보임)
- 풀너비 차트(`.full`)는 탭당 핵심 1개만 허용

## 카드 계층
- `.chart-card.primary` — 메인 인사이트 카드: border 없음, `background: rgba(14,12,10,0.04)`
- `.chart-card` — 일반 카드 (기본)
- `.chart-card.muted` — 보조/참고 카드: `opacity: 0.75`

## 타이포 규칙
- 차트 섹션 제목(cc-title): `font-weight: 700; font-family: 'Inter', sans-serif` — 한글 포함 시 serif 절대 금지
- Instrument Serif italic은 영문 전용 — 대시보드 헤더 영문 부제, method-btn 영문 텍스트에만 제한 사용
- KPI 수치: `font-family: 'Barlow', sans-serif; font-weight: 600`
- 차트 내부 레이블·수치: `font-family: 'JetBrains Mono', monospace`
- 차트 안 일반 텍스트: `font-family: 'Inter', sans-serif`

## 여백 리듬
- 섹션 간 간격: `margin-bottom: 28px` 고정
- 카드 패딩: `padding: 20px` 고정

## 차트 SVG 공백 일관성 규칙
- SVG는 항상 `width:100%; height:auto; max-height:none` — 카드 너비에 꽉 차게
- SVG viewBox 내부 여백: 상하 `10px`, 좌우 `축 레이블 있을 때 40~70px / 없을 때 8px`
- 차트 플롯 영역(실제 데이터 그리는 공간)은 viewBox 면적의 **80% 이상** 차지해야 함
- 축 레이블·범례는 viewBox 하단 20px 이내에 배치
- 카드 제목(cc-title) → cc-desc → SVG 순서 고정, SVG 위 여백 `margin-top: 8px`

## 색상 절제
- 하나의 탭에 강조색 최대 2가지
- `#35c995`(좋음) / `#ef7d86`(나쁨) / `#efb05d`(경고) — 데이터에 해당하는 것만 사용

## 폰트
- `Barlow`, `Instrument Serif`, `Inter`, `JetBrains Mono` (Google Fonts)

## 금지
- `border-left` 등 한쪽 변만 있는 border
- KPI 수치에 색상 적용
- 기본 이모지 사용 (📊 📈 💰 🎯 ⚡ 등 유니코드 이모지 전면 금지)
- 아이콘이 필요하면 반드시 인라인 SVG로 대체
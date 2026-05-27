# AI Pipeline Kit

CSV 파일 하나를 넣으면 7개의 AI 에이전트가 순서대로 실행되어 **EDA → 문제 정의 → KPI 설계 → 인사이트 분석 → 인터랙티브 대시보드 → 임원 보고서**까지 자동으로 생성하는 분석 파이프라인입니다.

---

## 파이프라인 구조

```
runs/latest/input/  ←  CSV 파일을 여기에 넣기
        ↓
@data-ingestion  →  01_dataset_profile.md
@eda             →  02_eda_report.md
@problem         →  03_problem_definition.md
@metrics         →  04_kpi_summary.md
@analysis        →  05_analysis_report.md
@dashboard       →  06_dashboard.html
@report          →  07_executive_report.md
        ↓
runs/latest/outputs/
```

| 단계 | 에이전트 | 산출물 | 내용 |
|------|---------|--------|------|
| 1 | `@data-ingestion` | `01_dataset_profile.md` | 컬럼 구조, 결측치, 데이터 품질 진단 |
| 2 | `@eda` | `02_eda_report.md` | 일변량·다변량 분석, 핵심 발견 |
| 3 | `@problem` | `03_problem_definition.md` | MECE 원인 계층, 핵심 분석 질문 |
| 4 | `@metrics` | `04_kpi_summary.md` | KPI 정의·계산식·현재값 |
| 5 | `@analysis` | `05_analysis_report.md` | 인사이트, 방법론 근거, 액션 |
| 6 | `@dashboard` | `06_dashboard.html` | 4탭 인터랙티브 대시보드 (순수 SVG) |
| 7 | `@report` | `07_executive_report.md` | 임원용 요약 보고서 |

---

## 시작 방법

### 1. 데이터 파일 배치

```
runs/latest/input/ 폴더에 분석할 파일을 넣습니다.
CSV, Excel, JSON, TSV, PDF 모두 가능합니다.
```

### 2. Claude Code에서 실행

```
/run-pipeline
```

또는 채팅창에서:

```
파이프라인 실행해줘
```

### 3. 결과 확인

```
runs/latest/outputs/ 폴더에 7개 파일이 생성됩니다.
06_dashboard.html 을 브라우저로 열면 인터랙티브 대시보드를 볼 수 있습니다.
```

---

## 예시 출력 — German Credit Dataset

이 레포에는 [UCI German Credit Dataset](https://archive.ics.uci.edu/dataset/144/statlog+german+credit+data) (1,000건) 분석 결과가 포함되어 있습니다.

**핵심 발견 3가지:**

1. **재무 중간층 역설** — 재무취약 집단(little+NA, 19.1%)이 아닌 moderate+moderate 그룹(n=47)이 평균 4,213 DM으로 전체 최고. "취약층 = 고액대출" 가설 기각.

2. **목적의 독립 설명력** — 저축 상태를 통제한 후에도 business(4,399 DM) vs radio/TV(2,351 DM) = 1.87배 격차 유지. 목적별 한도 차등화 정책이 재무상태와 별개로 유효.

3. **주거형태의 독립 예측력** — 동일 저축(little) 조건에서 free 거주자(4,545 DM)가 자가 소유자(3,021 DM)보다 50.4% 높음. 주거형태는 저축 구성의 반영이 아닌 독립 변수.

**대시보드 미리보기:**

> `runs/latest/outputs/06_dashboard.html` — 4탭 인터랙티브 대시보드
> - 탭 1: 재무 세그먼트 분석
> - 탭 2: 목적별 대출 규모 (5.5배 스프레드)
> - 탭 3: 주거형태 독립 효과 (+50.4%)
> - 탭 4: 포트폴리오 전체 구조 (r=0.625 산점도)

---

## 폴더 구조

```
ai-pipeline-kit/
├── CLAUDE.md                     # 오케스트레이터 지시문
├── dashboard-sample.html         # 대시보드 베이스 템플릿
├── german_credit_data.csv        # 예시 데이터
├── .gitignore
├── .claude/
│   ├── agents/                   # 7개 전문 에이전트 지시문
│   │   ├── data-ingestion.md
│   │   ├── eda.md
│   │   ├── problem.md
│   │   ├── metrics.md
│   │   ├── analysis.md
│   │   ├── dashboard.md
│   │   └── report.md
│   ├── rules/
│   │   ├── analysis-principles.md  # 분석 품질 원칙
│   │   └── design-system.md        # 대시보드 디자인 시스템
│   └── skills/
│       └── run-pipeline/           # /run-pipeline 스킬
└── runs/
    └── latest/
        ├── input/                  # 분석할 파일을 여기에
        └── outputs/                # 생성된 7개 결과물
```

---

## 주요 특징

- **체크포인트 시스템** — 이미 완료된 단계는 자동 스킵, 실패한 단계부터 재시작
- **순수 SVG 차트** — CDN 라이브러리 없이 오프라인에서도 대시보드 작동
- **도메인 독립** — 커머스·금융·의료·물류 등 어떤 도메인 데이터에도 적용 가능
- **개별 재실행** — `"@eda 만 다시 실행해줘"` 로 특정 단계만 재실행 가능

---

## 요구사항

- [Claude Code](https://claude.ai/code) (CLI 또는 데스크탑 앱)
- 분석할 데이터 파일 (CSV / Excel / JSON / TSV / PDF)

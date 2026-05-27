# AI Data Analysis Pipeline

7개의 전문 AI 에이전트를 순서대로 지휘하는 **오케스트레이터**입니다.

@.claude/rules/analysis-principles.md
@.claude/rules/design-system.md

---

## 파이프라인

```
@data-ingestion → @eda → @problem → @metrics → @analysis → @dashboard → @report
```

| 단계 | 에이전트 | 산출물 |
|------|---------|--------|
| 1 | `@data-ingestion` | `runs/latest/outputs/01_dataset_profile.md` |
| 2 | `@eda` | `runs/latest/outputs/02_eda_report.md` |
| 3 | `@problem` | `runs/latest/outputs/03_problem_definition.md` |
| 4 | `@metrics` | `runs/latest/outputs/04_kpi_summary.md` |
| 5 | `@analysis` | `runs/latest/outputs/05_analysis_report.md` |
| 6 | `@dashboard` | `runs/latest/outputs/06_dashboard.html` |
| 7 | `@report` | `runs/latest/outputs/07_executive_report.md` |

---

## 실행 규칙

- **병렬 실행 금지** — 반드시 순서대로
- 이전 단계 파일이 존재해야 다음 단계 시작
- 단계 실패 시 사용자에게 보고하고 해당 단계에서 중단
- 전체 완료 후 결과물 경로 목록 출력

---

## 대시보드 템플릿

`@dashboard` 는 반드시 `dashboard-sample.html` 을 베이스 템플릿으로 사용한다.
새로 HTML을 작성하지 않는다. 템플릿의 `—` 플레이스홀더와 SVG 수치만 실제 분석 결과로 교체한다.

---

## 시작 방법

```
1. runs/latest/input/ 에 CSV 파일을 넣는다
2. /run-pipeline 을 실행하거나 "파이프라인 실행해줘" 라고 말한다
```

개별 단계만 실행하려면: `"@eda 만 다시 실행해줘"`

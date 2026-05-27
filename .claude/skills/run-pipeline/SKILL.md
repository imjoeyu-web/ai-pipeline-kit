---
name: run-pipeline
description: 데이터 파일을 받아 7단계 AI 분석 파이프라인을 순서대로 실행하고 최종 대시보드와 보고서를 생성합니다. CSV·Excel·JSON·TSV·PDF 등 형식 무관.
argument-hint: (없음 — runs/latest/input/ 에 데이터 파일이 있어야 합니다)
tools: Read, Write, Bash, Glob, Agent
---

# /run-pipeline

`runs/latest/input/` 의 데이터 파일을 받아 7단계 분석 파이프라인을 순차 실행합니다.
CSV, Excel, JSON, TSV, PDF 등 형식 무관 — `@data-ingestion`이 자동 감지합니다.

## 실행 전 체크

1. `runs/latest/input/` 에 파일이 존재하는지 확인한다
2. 없으면 사용자에게 안내하고 **즉시 중단**한다:
   > "runs/latest/input/ 폴더에 데이터 파일을 넣어주세요. (CSV, Excel, JSON, TSV, PDF 모두 가능)"
3. Run ID 파일을 생성한다:
   ```bash
   date +%s > runs/latest/.run_id
   ```

## 체크포인트 규칙

각 단계 실행 **전** 아래 조건을 확인한다.

- 산출물 파일이 존재하고, `.run_id` 파일보다 **새것**이면 → `✅ STEP N — 완료 (skip)` 출력 후 건너뜀
- 파일이 없거나 `.run_id`보다 **오래됐으면** → 에이전트 실행

**확인 방법 (bash):**
```bash
OUTPUT="runs/latest/outputs/01_dataset_profile.md"
RUN_ID="runs/latest/.run_id"

if [ -f "$OUTPUT" ] && [ "$OUTPUT" -nt "$RUN_ID" ]; then
  echo "✅ STEP 1 — 완료 (skip)"
else
  # 에이전트 실행
fi
```

> **강제 재실행:** 사용자가 "처음부터 다시"라고 하면 `.run_id`를 삭제하거나 `runs/latest/outputs/` 를 비운 뒤 재실행한다.

## 단계별 실행 순서

각 단계는 체크포인트 확인 후, 실행이 필요한 경우에만 에이전트를 호출한다.

```
STEP 1 → @data-ingestion  출력: 01_dataset_profile.md
STEP 2 → @eda             출력: 02_eda_report.md
STEP 3 → @problem         출력: 03_problem_definition.md
STEP 4 → @metrics         출력: 04_kpi_summary.md
STEP 5 → @analysis        출력: 05_analysis_report.md
STEP 6 → @dashboard       출력: 06_dashboard.html
STEP 7 → @report          출력: 07_executive_report.md
```

## 단계 완료 확인

에이전트 실행 후 산출물 파일 존재를 확인한다.
파일이 없으면 해당 단계 실패로 간주하고 사용자에게 보고 후 중단한다.

## 완료 메시지

전체 성공 시 아래 형식으로 출력한다. skip한 단계는 `(cached)` 표시:

```
✅ 파이프라인 완료

📄 runs/latest/outputs/01_dataset_profile.md  (cached)
📄 runs/latest/outputs/02_eda_report.md        (cached)
📄 runs/latest/outputs/03_problem_definition.md
📄 runs/latest/outputs/04_kpi_summary.md
📄 runs/latest/outputs/05_analysis_report.md
🌐 runs/latest/outputs/06_dashboard.html
📄 runs/latest/outputs/07_executive_report.md
```

# Process Mining (D1)

## Purpose
Explain **why** cases stall: compare actual event trails with the prescribed route and rank waiting-time hotspots.

## Event log
From `stage_history`: `case_id = application_id`, `activity = stage`, `timestamp = entered_at` (and `exited_at` for durations), plus attributes `region`, `officer_id`, `category`. Export with pm4py's dataframe formatter.

## Reference model
Built from `routes.yaml` per category (a Petri net, or a simple set of allowed directly-follows pairs for a lightweight check).

## Analyses
| Analysis | Method | Output |
|---|---|---|
| Conformance | Token replay (fast) or alignments (precise) | Per-case fitness, list of skipped / unexpected steps |
| Rework loops | Count repeated `DEFICIENCY → RE_SCRUTINY` cycles per case | Histogram, cases with ≥3 loops |
| Bottlenecks | Mean/median/p90 waiting time per stage, officer, region | Ranked table |
| Variants | Distinct paths and their frequency | Top variants |

## API
`GET /analytics/conformance`, `GET /analytics/bottlenecks` (leadership roles; scoped by region for regional heads).

## UI
Process diagram highlighting slow edges and rework loops; deviation list linking to cases; "path diff" for one case vs the reference.

## Evaluation
Inject known deviations into synthetic logs (DEF-05, DEF-06); report **detected / injected** and false positives (`docs/experiments/EXP-03-process-mining-detection.md`).

## Limits
Synthetic logs; real logs may have missing timestamps, parallel activities and manual back-dating. Findings are "may need attention" signals, not blame.

## Fallback
If pm4py is unavailable or too slow: directly-follows counts and waiting-time stats computed in SQL/pandas.

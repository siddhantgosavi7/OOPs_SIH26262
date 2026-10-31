# Algorithms & ML Methodology

> **Principle:** use the simplest method that works; every technique needs a measurable purpose. We make **no accuracy claims** until tested.

## Method table
| Component | Method | Needs training data? | Why this choice |
|---|---|---|---|
| Rule engine | Deterministic rules (YAML/JSON) | No | Explainable; auditable |
| OCR / field extraction (optional) | Tesseract / PaddleOCR + regex | No (off-the-shelf) | Extraction only; rules decide |
| EVC scheduling | OR-Tools CP-SAT | No | Hard constraints (availability, COI, load) |
| Priority score | Weighted formula (config) | No | Transparent |
| Process mining (D1) | pm4py conformance + waiting-time analysis | No | Real insight from event logs |
| Simulation (D2) | SimPy discrete-event | Parameters only | What-if planning |
| Delay-risk (optional) | Gradient boosting vs baseline | Yes (synthetic) | Only if it beats the baseline on held-out data |

## Priority score (transparent formula)
```
priority = w1 * (days_in_stage / sla_days)
         + w2 * open_major_findings_normalised
         + w3 * institution_waiting_days_normalised
```
Weights come from configuration. The UI shows each term's contribution ("why this priority").

## EVC scheduling model (sketch)
- Variables: `x[e, v] ∈ {0,1}` — expert *e* assigned to visit *v*; `d[v]` — visit date from the candidate set.
- Hard constraints: panel size; availability on date; **COI** (expert's home institution ≠ applicant); no double-booking on the same date; max visits per expert per period.
- Objective: minimise load imbalance + earliest-date penalty.
- Time limit with fallback to earliest-deadline-first.

## Process mining (D1)
1. Export `stage_history` to an event log: case = application, activity = stage, timestamp = `entered_at`.
2. Discover or load the **reference model** from `routes.yaml`.
3. Conformance: token replay (fast) or alignments (precise) → deviations (skipped/extra steps).
4. Performance: waiting time per stage, officer and region → ranked bottlenecks.
5. Rework loops: count repeated `DEFICIENCY ↔ RE_SCRUTINY` visits.

## Simulation (D2)
Discrete-event model: arrivals → stage queues → service by officers with capacity limits. Run N replications with fixed seeds; report mean and confidence interval; show the parameter table beside every result.

## Delay-risk model (optional)
- Only build if time permits **and** it beats the baseline "historical breach rate" / formula `days_in_stage / sla_days`.
- Time-based train/test split (no leakage); report AUC and PR vs baseline.
- Output wording: "may need attention"; never used to penalise a person or institution.

## Evaluation plan
| Item | Metric | Dataset | Result |
|---|---|---|---|
| Rule engine | seeded defects found / missed / false alarms | synthetic gold set | TBD |
| OCR extraction | field-level accuracy, confidence calibration | synthetic docs | TBD |
| EVC scheduler | constraint violations (must be 0), solve time | synthetic | TBD |
| Conformance (D1) | injected deviations detected | synthetic logs | TBD |
| Simulator (D2) | variance across runs; sensitivity to parameters | synthetic | TBD |
| Delay-risk | AUC / PR vs baseline, time-based split | synthetic | TBD |

Protocols: `docs/experiments/`. Results go in `RESULTS.md` only after measurement.

## Fallbacks
Low-confidence extraction → manual review. Solver timeout → earliest-deadline-first. Model missing → formula `days_in_stage / sla_days`.

## Responsible use
Scores and predictions are "may need attention" signals. Synthetic-data metrics demonstrate the pipeline, not real-world accuracy.

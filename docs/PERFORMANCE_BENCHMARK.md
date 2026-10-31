# Performance Benchmark

> No performance claim is made until it appears in the **Results** table with a date, commit and hardware description.

## Questions to answer
1. p95 latency of list endpoints with 10k synthetic cases.
2. Wall-clock time of the daily job on 10k cases.
3. Rule-evaluation time per case.
4. EVC solve time (p50/p95) and fallback frequency.
5. Process-mining runtime on 10k cases.
6. Simulation runtime for 200 replications × 90 days.

## Method
- Hardware/software recorded (CPU, RAM, Docker versions, Postgres settings).
- Dataset: `seed.py --applications 10000 --seed 42`.
- API load: locust or k6, ramp to a stated concurrency, 5-minute steady state.
- Warm-up run discarded; 3 repetitions; report median and range.
- DB: check `EXPLAIN ANALYZE` of the slowest queries; add indexes (e.g. `applications(stage, region)`, `stage_history(application_id, exited_at)`, `documents(valid_until)`, `undertakings(due_date, status)`).

## Commands (examples)
```bash
python backend/scripts/seed.py --applications 10000 --seed 42
k6 run tests/perf/list_applications.js
python tests/perf/time_daily_job.py
```

## Targets
Set targets **after** the first measurement; do not pre-commit to numbers.

## Results
| Metric | Dataset | Concurrency | Result | Date | Commit | Hardware |
|---|---|---|---|---|---|---|
| List p95 | 10k | | TBD | | | |
| Daily job | 10k | — | TBD | | | |
| Rule eval / case | — | — | TBD | | | |
| EVC solve p95 | 500 requests | — | TBD | | | |
| Conformance runtime | 10k cases | — | TBD | | | |
| Simulation runtime | 200 runs | — | TBD | | | |

## Scaling notes (qualitative)
Stateless API (scale horizontally), queue-based jobs, indexed reporting views, pagination everywhere. Real national scale needs real load testing — listed in `ROADMAP.md`.

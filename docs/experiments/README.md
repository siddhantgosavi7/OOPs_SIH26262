# Experiments

Reproducible evaluation protocols. Each experiment states its hypothesis, data, procedure, metrics and a results table that stays `TBD` until a real run fills it in. Results are copied to `../RESULTS.md`.

| ID | Experiment | Feature | Key metric |
|---|---|---|---|
| EXP-01 | Rule engine gold set | C2 | Defects found / missed / false alarms |
| EXP-02 | EVC scheduler constraints | C4 | Violations (must be 0), solve time |
| EXP-03 | Process-mining detection | D1 | Injected deviations detected |
| EXP-04 | Simulator sensitivity | D2 | Variance across runs, parameter sensitivity |
| EXP-05 | Idempotency and audit tamper | C7, C8 | Duplicate alerts (must be 0), tamper detected |

## Template
```
# EXP-NN · Title
- Hypothesis:
- Data (generator, seed, size):
- Procedure (commands):
- Metrics:
- Pass criteria:
- Results (date, commit, numbers):
- Threats to validity:
```

## Rules
1. Fixed seeds; record the git commit hash with every result.
2. Report negative results too.
3. Synthetic data demonstrates the pipeline; do not generalise to real AICTE data.

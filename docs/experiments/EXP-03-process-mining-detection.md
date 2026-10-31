# EXP-03 · Process-Mining Detection
- **Hypothesis:** Conformance checking detects injected skipped stages and rework loops, and ranks the slowest stage correctly.
- **Data:** `make_demo_logs.py --months 3 --seed 42` with injected deviations (DEF-05 skipped stage, DEF-06 loops ≥3) at known rates (e.g. 5% and 3%), plus one stage given a deliberately long waiting time.
- **Procedure:** export event log → run conformance (token replay, then alignments on a sample) → compare flagged cases to the manifest; compare ranked bottlenecks with the injected slow stage.
- **Metrics:** detected / injected, false positives, rank of the injected bottleneck, runtime for 10k cases.
- **Pass criteria:** thresholds fixed *before* the run and recorded here.
- **Results:** **TBD**
- **Threats to validity:** Synthetic logs are cleaner than real ones (no missing timestamps, no back-dating).

# Results

> **Only measured numbers go here.** Each row must link to the experiment/test run that produced it. Empty = not measured yet.

## Summary table
| Claim | Metric | Value | Source | Date | Commit |
|---|---|---|---|---|---|
| Rule engine finds seeded defects | found / seeded | TBD | EXP-01 | | |
| Rule engine false alarms | count | TBD | EXP-01 | | |
| EVC constraint violations | count (must be 0) | TBD | EXP-02 | | |
| Conformance detects injected deviations | detected / injected | TBD | EXP-03 | | |
| Simulator scenario stability | CI width / rank correlation | TBD | EXP-04 | | |
| Duplicate alerts on job re-run | count (must be 0) | TBD | EXP-05 | | |
| Audit tamper detection | detected? | TBD | EXP-05 | | |
| Daily job on N cases | seconds | TBD | PERFORMANCE_BENCHMARK | | |

## Demo-day numbers (copy from above)
- Seeded defects found / missed: `__ / __`
- EVC constraint violations: `0` *(must hold)*
- Daily job on `N` synthetic cases: `__ s`
- Idempotency: running job twice → `0` duplicate alerts

## Interpretation rules
1. Say "on synthetic data" every time.
2. Report failures and surprises.
3. Don't round up; don't compare against unmeasured baselines.

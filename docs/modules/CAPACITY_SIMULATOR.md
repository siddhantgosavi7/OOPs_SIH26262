# Capacity What-If Simulator (D2)

## Purpose
Let leadership compare scenarios ("add 2 officers in Region X", "shorten SLA at SCRUTINY") **before** acting, using a discrete-event simulation with visible assumptions.

## Model
- **Arrivals:** per-category arrival process (Poisson by default; parameter = rate per day).
- **Stages:** queue + service time distribution per stage (lognormal/gamma; parameters from config).
- **Resources:** officers per region/stage with capacity (cases in parallel) and working calendar.
- **Policy:** FIFO or priority-score ordering.
- **Outputs:** mean/median/p90 time in system, queue lengths, SLA breach rate, officer utilisation.

## Implementation
SimPy `Environment`, `Resource` per officer pool. Run `N` replications with seeds `seed, seed+1, …`; report mean and 95% confidence interval.

## API
`POST /simulation/run`
```json
{ "horizon_days": 90, "runs": 200, "seed": 42,
  "scenario": { "region": "X", "add_officers": 2, "stage": "SCRUTINY" } }
```
Response includes `baseline`, `scenario`, `delta`, and a `parameters` table that the UI must display beside the results.

## Honesty rules
- Label every result **"illustrative — parameters are assumed"** until calibrated with real data.
- Show a sensitivity analysis (vary key parameters ±X%) to show how fragile the conclusion is.
- Never present numbers as predictions about real AICTE performance.

## Evaluation
Variance across runs; sensitivity to parameters; sanity checks (more officers should not increase mean wait) → `docs/experiments/EXP-04-simulator-sensitivity.md`.

## Fallback
Analytical M/M/c estimate if SimPy is not ready; label it clearly.

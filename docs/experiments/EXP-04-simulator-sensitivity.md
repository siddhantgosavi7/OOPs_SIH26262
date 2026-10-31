# EXP-04 · Simulator Sensitivity
- **Hypothesis:** Scenario conclusions are stable across seeds and show sensible monotonic behaviour (more officers do not increase mean wait).
- **Data:** Parameter table in `CONFIGURATION.md` (assumed distributions).
- **Procedure:** run 200 replications baseline and each scenario; repeat with key parameters varied ±20%.
- **Metrics:** mean and 95% CI of time-in-system and breach rate; rank correlation of scenario ordering across parameter variations.
- **Pass criteria:** monotonic sanity checks hold; CI widths reported.
- **Results:** **TBD**
- **Threats to validity:** Every input is assumed; output is **illustrative** only.

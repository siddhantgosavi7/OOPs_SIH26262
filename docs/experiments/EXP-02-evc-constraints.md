# EXP-02 · EVC Scheduler Constraints
- **Hypothesis:** The scheduler never proposes a double-booked, unavailable or conflicted expert, and finds a feasible panel when one exists.
- **Data:** 60 synthetic experts, 500 random visit requests, planted COI (DEF-07) and unavailability (DEF-08) cases.
- **Procedure:**
  ```bash
  python tests/gold/run_evc_eval.py --requests 500 --seed 42
  ```
  For each proposal, an independent checker re-verifies every hard constraint. Also run 50 concurrent confirmations on the same expert/date.
- **Metrics:** constraint violations (**must be 0**), infeasible rate, solve-time p50/p95, fallback-heuristic usage, concurrent-confirm 409 count.
- **Pass criteria:** violations = 0; exactly one confirm wins per contested slot.
- **Results:** **TBD**
- **Threats to validity:** The checker shares our constraint definitions; real committees may have extra constraints.

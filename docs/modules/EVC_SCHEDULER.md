# EVC Scheduler (C4)

## Purpose
Propose conflict-free Expert Visit Committee panels and dates. The regional head makes the final confirmation (human decision).

## Inputs
Application (category, institution, region, required expertise), date window, panel size, expert pool (region, expertise, load, home institution), unavailability, existing assignments.

## Hard constraints
1. Panel size exactly as configured.
2. Expert is available on the visit date (not in `expert_unavailability`).
3. **No conflict of interest:** expert's home institution ≠ applicant (extend with other COI relations if modelled).
4. No double-booking on the same date.
5. Max visits per expert per period (configurable).
6. Required expertise covered by the panel.

## Objective (soft)
Minimise load imbalance across experts; prefer earlier dates; prefer same-region experts to limit travel.

## Algorithm
1. Prefilter candidates by region/expertise/availability/COI (cheap SQL).
2. Build CP-SAT model (`x[e,d]` booleans, constraints above), time limit (e.g. 5 s).
3. Return top-K proposals, each with an `explain` object (excluded experts and why, load figures).
4. If the solver times out or is infeasible: fall back to earliest-deadline-first greedy assignment; if still infeasible → return `NO_FEASIBLE_PANEL` with reasons.

## Confirmation
`POST /evc/{id}/confirm` re-validates every constraint **inside a transaction with row locks** on the chosen experts; a clash returns `409 EVC_CLASH`.

## Explainability
Every proposal lists: who, why chosen (load, expertise match), who was excluded and why (COI, unavailable, over-capacity).

## Tests / invariants
- Over N seeded requests: **0** double-bookings; **0** COI experts proposed.
- Two concurrent confirmations for the same expert/date → one 409.
- Infeasible input → clear message, not an exception.
- Solve time distribution recorded in `PERFORMANCE_BENCHMARK.md`.

# Solution Overview

## Vision
Make AICTE approval **transparent, explainable and plannable** while keeping every regulatory decision with a human officer.

## Design principles
1. **Decision support, never decision maker.** Officers approve or reject; the system recommends and records.
2. **Configuration, not code.** Stages, SLAs, owners, reminders, escalation ladders and rules live in tables and are versioned.
3. **Explainable.** Every flag shows rule ID, rule version, evidence and (when verified) APH clause.
4. **Audit by default.** Every state change appends to a hash-chained log.
5. **Honest data.** Synthetic data and labelled mock adapters only.

## Three layers + trust
| Layer | Contents | Module docs |
|---|---|---|
| 1. Reliable core | Workflow state machine, rule engine, SLA/pendency, EVC scheduler, undertakings and validity, alerts, audit chain | `modules/WORKFLOW_ENGINE.md`, `RULE_ENGINE.md`, `SLA_AND_PENDENCY.md`, `EVC_SCHEDULER.md`, `UNDERTAKINGS_AND_VALIDITY.md`, `ALERTS_AND_ESCALATION.md`, `AUDIT_CHAIN.md` |
| 2. Process intelligence (D1) | Conformance checking, rework loops, bottleneck ranking | `modules/PROCESS_MINING.md` |
| 3. Leadership intelligence (D2, D4) | Capacity simulator, obligation graph | `modules/CAPACITY_SIMULATOR.md`, `OBLIGATION_GRAPH.md` |
| Trust (D3) | Signed, QR-verifiable approval record | `modules/CREDENTIALS.md` |

## End-to-end flow
1. Institution submits an application (synthetic) → workflow creates a case and routes it by category.
2. Rule engine evaluates checklist rules → findings with citations.
3. Officer reviews the ranked queue → records the scrutiny outcome (**human**).
4. Deficiency → institution responds → re-scrutiny round.
5. EVC requested → scheduler proposes conflict-free panels → officer confirms (**human**).
6. Decision recorded (**human**) → credential issued (demo) → undertakings created if conditional.
7. Daily job: SLA breaches, expiries, escalations → alerts.
8. Leadership dashboard: funnel, ageing, bottlenecks, simulation.

## Human-in-the-loop points
Scrutiny outcome, EVC confirmation, approval decision, waiver of undertakings, rule changes (admin).

## Out of scope
Real AICTE/NSWS integration, real bank/DigiLocker/APAAR verification, final regulatory decisions, real personal data.

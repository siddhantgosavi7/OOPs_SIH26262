# Features & Differentiators

## A. Core features (table stakes)
| ID | Feature | Acceptance test |
|---|---|---|
| C1 | Configurable workflow + role-gated transitions | Illegal transition returns `409 INVALID_TRANSITION` with reason |
| C2 | APH rule engine (versioned, clause-cited) | Gold set of seeded defects: report found / missed / false alarms |
| C3 | SLA + pendency ageing + priority | Breach shown within one scheduler cycle |
| C4 | EVC scheduler (availability, COI, load) | No expert double-booked; COI expert never proposed |
| C5 | Undertakings lifecycle | PENDING → SUBMITTED → VERIFIED / OVERDUE / WAIVED |
| C6 | Document validity tracking | Reminders at configured offsets, deduplicated |
| C7 | Alerts + escalation ladder | Re-running the daily job creates no duplicates |
| C8 | Hash-chained audit log | Editing an old row makes `/audit/verify` fail |
| C9 | RBAC + row-level scoping | Region officer cannot read another region's cases |

## B. Differentiators
### D1 · Process-mining conformance & bottleneck root cause
- **Does:** compares each case's event trail with the prescribed route; reports skipped stages, rework loops and waiting-time hotspots.
- **Real problem:** dashboards show *how many* are late, not *why*.
- **Implementation:** event log from `stage_history`; pm4py conformance (token replay / alignments); bottleneck = waiting time per stage / officer / region.
- **Evidence:** synthetic logs with injected deviations; report detection rate.
- **Limit:** synthetic logs; real logs may differ.

### D2 · Capacity what-if simulator
- **Does:** simulates arrivals, stage service times and officer capacity; compares scenarios (add officers, change SLA).
- **Implementation:** SimPy; parameters from assumed distributions, clearly labelled.
- **Evidence:** scenario table with confidence intervals over N runs.
- **Limit:** illustrative until calibrated with real data.

### D3 · Verifiable approval credential
- **Does:** issues a signed JSON/PDF record with a QR; a public page verifies signature and status; supports revocation.
- **Implementation:** Ed25519 signatures; status list; demo keypair.
- **Evidence:** tampered JSON/PDF fails verification.
- **Limit:** not an official AICTE credential; key management out of scope.

### D4 · Institution obligation graph
- **Does:** models documents, undertakings and approvals as a graph; answers "what is at risk if X lapses?".
- **Implementation:** relational edges + PostgreSQL recursive CTEs; optional graph view in the UI.
- **Evidence:** seeded scenario where an expiring lease flags dependent approvals.

### D5 · Explainability
Every flag exposes `rule_id`, `rule_version`, evidence fields, confidence (when OCR-extracted) and "why this priority".

## C. Supporting (not innovation)
Login/RBAC, notifications, reports / CSV export, bilingual labels (English + Hindi, optional).

## D. Priority
- **MVP:** C1–C9.
- **Advanced:** D1, D5.
- **Stretch:** D2, D3, D4 — pick by remaining time.

> **Ship fewer features fully rather than many half-built.**

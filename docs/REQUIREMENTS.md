# Requirements

## 1. Functional requirements
| ID | Requirement | Feature |
|---|---|---|
| FR-01 | Create, view and list applications scoped to the user | C9 |
| FR-02 | Configurable stages with role-gated transitions; illegal moves rejected with 409 | C1 |
| FR-03 | Evaluate versioned rules and store findings with rule ID, version and evidence | C2 |
| FR-04 | Officers accept or dismiss findings with a reason | C2 |
| FR-05 | Compute SLA due dates, ageing and a transparent priority score | C3 |
| FR-06 | Propose conflict-free EVC panels; confirm with clash detection | C4 |
| FR-07 | Track undertakings through PENDING / SUBMITTED / VERIFIED / OVERDUE / WAIVED | C5 |
| FR-08 | Track document validity with reminders at configured offsets | C6 |
| FR-09 | Create deduplicated alerts and run an escalation ladder | C7 |
| FR-10 | Append every state change to a hash-chained audit log; verify on demand | C8 |
| FR-11 | Enforce RBAC and region/institution scoping server-side | C9 |
| FR-12 | Process-mining conformance and bottleneck views | D1 |
| FR-13 | Capacity what-if simulation with visible parameters | D2 |
| FR-14 | Issue and publicly verify signed approval records; support revocation | D3 |
| FR-15 | Answer "what is at risk if X lapses?" | D4 |
| FR-16 | Every flag and priority is explainable | D5 |

## 2. Non-functional requirements
| ID | Requirement |
|---|---|
| NFR-01 | Findings never change stage automatically; only an officer action does |
| NFR-02 | Daily job and alert creation are idempotent |
| NFR-03 | Timestamps stored in UTC, displayed in IST |
| NFR-04 | All secrets in environment variables; none committed |
| NFR-05 | Uploads: type/size whitelist, SHA-256 on upload, never executed |
| NFR-06 | Rule or extraction failure routes to manual review, never auto-rejects |
| NFR-07 | Public verify page and login are rate limited |
| NFR-08 | One-command local start (`docker compose up --build`) |
| NFR-09 | Performance targets are measured and reported, not assumed |
| NFR-10 | Accessibility baseline: keyboard navigation, labelled inputs, contrast |

## 3. Constraints
- Synthetic data only; mock adapters only.
- No claim of regulatory authority.
- Stage names, SLAs and escalation timings are placeholders until verified.

## 4. Environment requirements
| Item | Minimum |
|---|---|
| Docker + Docker Compose | Current stable |
| Python | 3.12 (if running without Docker) |
| Node.js | Current LTS |
| RAM | 4 GB for the demo stack (more for 10k-case benchmarks) |

## 5. Traceability
Each FR maps to a feature (C/D ID) in `FEATURES.md`, to tests in `TESTING_STRATEGY.md`, and to a status line in `COMPLETENESS_REPORT.md`.

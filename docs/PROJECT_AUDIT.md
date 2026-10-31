# Project Audit

A living self-review. Re-run before every milestone and before submission.

## 1. Scope & honesty audit
| Check | Status | Notes |
|---|---|---|
| README states "decision-support prototype; synthetic data; mocks" | ☐ | |
| No claim of uniqueness without evidence | ☐ | |
| No accuracy number without a run in RESULTS.md | ☐ | |
| Every rule has `clause_ref` or `ASSUMED` | ☐ | |
| Stage names/SLAs labelled as placeholders in UI | ☐ | |
| "SYNTHETIC" banner on every demo screen | ☐ | |

## 2. Competitor / landscape re-check (monthly)
| Date | Source searched | What we found | Action |
|---|---|---|---|
| | Public GitHub repos for SIH26262 | | |
| | AICTE portal public approval lookup | | |
| | Community slides / write-ups | | |

## 3. Technical audit
| Check | Status |
|---|---|
| All MVP invariants (TESTING_STRATEGY) pass | ☐ |
| CI green on `main` | ☐ |
| Fresh clone → `docker compose up` works | ☐ |
| No secrets in repo history | ☐ |
| Migrations apply from empty DB | ☐ |
| Docs match code (endpoints, tables, config keys) | ☐ |

## 4. Documentation audit
- [ ] Every doc in README index exists
- [ ] Links resolve (run a markdown link checker)
- [ ] Diagram stage names match `routes.yaml`
- [ ] COMPLETENESS_REPORT matches reality

## 5. Verification items (external)
- [ ] Official PS text pasted into PROBLEM_STATEMENT.md
- [ ] APH clauses verified in CLAUSE_MAP.md
- [ ] One conversation with an officer / ex-officer / domain expert recorded (even if brief)

## 6. Audit log
| Date | Auditor | Findings | Fixed? |
|---|---|---|---|
| | | | |

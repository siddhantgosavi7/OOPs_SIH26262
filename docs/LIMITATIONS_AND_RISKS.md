# Limitations & Risks

## Assumptions (validate before final)
- Stage names, SLA durations and escalation timings are placeholders.
- Officer/institution pain points are inferred from public documents, not interviews.
- Differentiators D1–D5 are **not verified as unique**; competitor research is ongoing.

## Not done / out of scope
- No real AICTE, NSWS, bank, DigiLocker or APAAR integration.
- No real personal or institutional data.
- No claim of regulatory authority; officers retain all decisions.
- DSC trust-chain verification not performed against a government trust chain.

## Known limitations
- Simulation and delay-risk outputs depend on synthetic parameters.
- OCR quality varies on poor scans.
- Rules cover only a subset of APH norms (listed in `CLAUSE_MAP.md`).
- Audit chain is tamper-evident, not tamper-proof against a DB superuser.
- Calendar-day SLAs; no holiday calendar.
- English-first UI (Hindi labels optional).

## Risk register
| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Overlap with other teams' features | High | Medium | Differentiate via D1–D5; polish core; re-check competitor repos |
| Rules misread from APH | Medium | High | Clause map + domain reviewer; mark ASSUMED rules |
| Over-scoping | High | High | Freeze MVP; stretch only after tests pass |
| No real data/integrations | Certain | Medium | Synthetic data; labelled mock adapters |
| Simulator seen as made-up numbers | Medium | Medium | Show parameters; label illustrative; sensitivity analysis |
| OCR errors | Medium | Low | Confidence thresholds; manual review |
| Demo failure | Medium | High | Recorded video; local fallback; rehearsal |
| Security misconfiguration | Low | High | Checklist, secrets hygiene, tests |
| Team member unavailable | Medium | Medium | Pair ownership of each module |
| Concurrency bugs (double-booking, chain fork) | Medium | High | Row locks, advisory locks, concurrency tests |

## Open questions
- [ ] Exact official SIH26262 text
- [ ] APH clause verification for each rule
- [ ] Reality check with one domain expert

# Roadmap

## MVP (hackathon prototype)
C1–C9 working end to end on synthetic data; demo scenes 1–3 and 6; docs and tests.

## Advanced
D1 process-mining dashboard; D5 explainability views; optional delay-risk model **only if** it beats the baseline.

## Stretch
D2 simulator; D3 credential + public verify; D4 obligation graph.

## Production (needs official approval / infrastructure)
- Integration with AICTE portal and NSWS through approved APIs.
- Real identity/KYC and DSC trust-chain verification.
- Security audit, VAPT, load testing, data-protection review.
- Calibration of the simulator with real (authorised) data.
- Accessibility (WCAG) and multilingual UI.
- Holiday-aware SLA calendar; email/SMS notifications.

## Suggested timeline *(adjust to the SIH schedule)*
| Phase | Focus | Exit criteria |
|---|---|---|
| Week 1 | Verify PS text and APH clauses; lock scope; DB + workflow + auth + audit | Steps 0–4 of IMPLEMENTATION_GUIDE |
| Week 2 | Rule engine, SLA, alerts, synthetic data | Steps 5–8; gold set running |
| Week 3 | EVC scheduler, undertakings, dashboards, frontend MVP | Steps 9–11; demo scenes 1–3 |
| Week 4 | Differentiators, tests, performance | Steps 12–14; RESULTS filled |
| Final days | Demo rehearsal, video, slides, freeze | SUBMISSION_CHECKLIST complete |

## Scope-cut order (if time runs short)
Cut in this order: D4 → D3 → D2 → optional OCR → delay-risk model → Hindi labels. Never cut tests, audit chain, RBAC or idempotency.

## After SIH
Validate with officers; calibrate with authorised data; pilot with one region.

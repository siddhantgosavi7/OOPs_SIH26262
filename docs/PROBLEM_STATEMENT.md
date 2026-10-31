# Problem Statement

## Official statement
**SIH26262** — *Integrated AICTE Approval Workflow Automation, Compliance Monitoring and Alert Management System* (AICTE · Software · Smart Automation).

> **TODO (before submission):** paste the exact official description from sih.gov.in. Only the first part of the text was visible through secondary sources during research.

## Background (from public sources)
- Applications for AY 2026-27 are submitted online on the AICTE web portal under the Approval Process Handbook (APH) 2024-25 to 2026-27; new institutions apply through NSWS.
- Processing includes Scrutiny / Re-Scrutiny and an Expert Visit Committee (EVC). Decisions are issued as a Letter of Approval, Letter of Deficiency or Letter of Rejection.
- EVC is required for intake increases and new courses. The Regulation Bureau arranges it; its officer is not a committee member.
- Documents carry digital signatures; an Affidavit 2 is required; fraudulent documents can lead to criminal action.

## Who is affected
| User | Needs |
|---|---|
| Scrutiny / bureau officers | Know what needs action today; avoid missed SLAs |
| Regional / national heads | See where regions or stages are slow; allocate capacity |
| Expert visit committee members | Fair, conflict-free scheduling |
| Institutions | Know status, deadlines, what blocks approval |
| Auditors | Trace who did what and when |
| Students / parents / universities | Confirm an institution's approval status |

## Problem breakdown
1. **Workflow visibility** — stage movement and pendency are hard to monitor across regions.
2. **Deadlines** — undertakings and document validity (e.g. lease) depend on manual follow-up.
3. **EVC logistics** — assembling conflict-free panels and dates.
4. **Escalation** — delays need predictable, auditable escalation.
5. **Trust** — decisions and overrides need a tamper-evident record.

## Verified vs assumed
| Statement | Status |
|---|---|
| Stages include scrutiny, re-scrutiny, EVC, committee decisions | Verified (APH text, secondary summaries) |
| Officers lack cross-region pendency analytics | **Assumption** — validate with an officer / ex-officer |
| Institutions miss undertaking deadlines | **Assumption** — validate |
| Specific delay numbers | **Unknown** — we claim none |

## Why it matters
Approval determines whether an institution may admit students for the year. Delay and inconsistency affect institutions and students; opacity reduces trust.

## Success criteria for this prototype
1. Every case has a visible stage, owner, due date and explainable priority.
2. Every rule flag cites a rule ID, version and (when verified) an APH clause.
3. No expert is double-booked and no conflicted expert is proposed.
4. Alerts are idempotent; the audit chain detects tampering.
5. Every regulatory decision remains with a human officer.

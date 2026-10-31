# Executive Summary

**ApprovalIQ** is a decision-support prototype for AICTE approval: a configurable workflow, a clause-cited rule engine, SLA and pendency tracking, conflict-free EVC scheduling, undertaking/expiry tracking, escalation alerts and a tamper-evident audit trail. On top of that reliable core it adds process mining, a capacity what-if simulator, an institution obligation graph and a verifiable approval credential.

## One-paragraph pitch
AICTE processes thousands of approvals each cycle through scrutiny, EVC inspection and committee decisions. Pendency, expiries and visits are tracked largely by hand across regions. ApprovalIQ makes the process transparent, explainable and plannable, while leaving every regulatory decision to a human officer.

## Key points
| Topic | Summary |
|---|---|
| Problem | Fragmented, manual tracking of approvals, deadlines, visits and escalations |
| Users | Officers, regional/national heads, EVC members, institutions, auditors, public verifiers |
| Core (table stakes) | Workflow, versioned rules, SLA, EVC scheduler, undertakings, alerts, audit chain, RBAC |
| Differentiators | D1 process mining, D2 simulator, D3 credential, D4 obligation graph, D5 explainability |
| Stack | React + TypeScript, FastAPI, PostgreSQL, Redis, pm4py, OR-Tools, SimPy, Docker Compose |
| Data | **Synthetic only**; every external integration is a labelled mock |
| Honesty | No accuracy or uniqueness claims without evidence |

## Scope statement
Decision-support prototype. It never approves or rejects an application. No integration with the real AICTE portal, NSWS, banks, DigiLocker or APAAR. Stage names, SLAs and escalation timings are configurable placeholders unless a clause reference says otherwise.

## What judges can verify
Five-scene demo with measurable results (seeded defects found, zero EVC constraint violations, zero duplicate alerts on re-run, audit tamper detection). See `DEMO_SCRIPT.md` and `RESULTS.md`.

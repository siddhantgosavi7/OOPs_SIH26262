# Datasets (Synthetic)

> All data is **synthetic**. No real institution, person or AICTE record is used. Every screen shows a "SYNTHETIC" banner in demo mode.

## Why synthetic
No authorised access to real AICTE data; synthetic data also lets us **inject known defects** and measure detection honestly.

## What we generate (`backend/scripts/seed.py`, `make_demo_logs.py`)
| Dataset | Size (default) | Contents |
|---|---|---|
| Institutions | 200 | Fake names ("SYNTHETIC INSTITUTE 001"), region, state, type |
| Users | ~30 | One per role per region + demo logins |
| Experts | 60 | Region, expertise tags, home institution (for COI), load |
| Applications | 2,000 (10,000 for benchmarks) | Category, stage, timestamps |
| Documents | ~3 per application | Type, SHA-256, `valid_until` (some deliberately short) |
| Stage history (event log) | 2–3 months | Normal paths + injected deviations |
| Undertakings | ~500 | Mixed statuses and due dates |

## Seeded defects (ground truth for the gold set)
| Defect ID | Description | Expected rule / finding |
|---|---|---|
| DEF-01 | Lease validity shorter than the configured minimum | `R-DOC-LEASE-VALIDITY` |
| DEF-02 | Missing required document type | `R-DOC-REQUIRED` |
| DEF-03 | Duplicate document hash across applications | duplicate flag |
| DEF-04 | Undertaking past due | `UNDERTAKING_OVERDUE` alert |
| DEF-05 | Stage skipped in event log | D1 conformance deviation |
| DEF-06 | Rework loop (3+ re-scrutiny rounds) | D1 loop detection |
| DEF-07 | Expert with COI in candidate pool | never proposed by scheduler |
| DEF-08 | Expert unavailable on candidate date | never proposed by scheduler |

A manifest (`tests/gold/manifest.json`) records which application contains which defect so evaluation scripts can compute found / missed / false-alarm counts.

## Generation rules
- Fixed random seed (`seed=42` by default) → reproducible runs.
- Waiting times drawn from simple, **assumed** distributions (e.g. lognormal); parameters stored in `docs/experiments/` and shown in the UI next to simulator output.
- No realistic personal names, phone numbers, emails or IDs; use obviously fake values.
- Dates relative to "today" at seed time, stored in UTC.

## Limits
- Synthetic distributions do not reflect real AICTE behaviour; metrics demonstrate the **pipeline**, not real-world accuracy.
- Real data would require an official data-sharing arrangement and a data-protection review.

## Reproduce
```bash
python backend/scripts/seed.py --institutions 200 --applications 2000 --seed 42
python backend/scripts/make_demo_logs.py --months 3 --seed 42
```

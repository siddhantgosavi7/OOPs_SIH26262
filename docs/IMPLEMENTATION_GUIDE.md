# Implementation Guide (build order)

Follow this order. Each step ends with a **definition of done** (DoD). Don't start a stretch feature until MVP tests pass.

## Step 0 — Repo, tooling, CI (½ day)
- Create the structure in `PROJECT_STRUCTURE.md`; add `ruff`, `black`, `pytest`, `eslint`, `vitest`.
- GitHub Actions: lint + unit + integration on PR.
- `.env.example`, `docker-compose.yml` (see `DEPLOYMENT.md`).
- **DoD:** `docker compose up` starts empty `api`, `web`, `postgres`, `redis`; CI is green.

## Step 1 — Database foundation (C1, C8, C9)
- Migrations 001–004 from `DATABASE_SCHEMA.md`.
- Revoke UPDATE/DELETE on `audit_entries` and `rules` for the app role.
- **DoD:** migrations apply to an empty DB; partial unique index test passes.

## Step 2 — Auth + RBAC + scoping (C9)
- `POST /auth/login` → JWT (short expiry). Dependency `current_user()` loads role + region + institution.
- Central `scope_query(user, query)` helper used by **every** list/detail query.
- **DoD:** test "Region A officer cannot read Region B case" passes (also via direct ID guessing).

## Step 3 — Audit chain (C8) — build early, everything uses it
```python
import hashlib, json

def canonical(payload: dict) -> bytes:
    return json.dumps(payload, sort_keys=True, separators=(",", ":"), default=str).encode()

def append(db, actor_id, action, entity, payload):
    last = db.execute("SELECT hash FROM audit_entries ORDER BY id DESC LIMIT 1 FOR UPDATE").scalar()
    prev = last or "0" * 64
    payload_hash = hashlib.sha256(canonical(payload)).hexdigest()
    h = hashlib.sha256((prev + payload_hash).encode()).hexdigest()
    db.execute(INSERT_AUDIT, dict(actor_id=actor_id, action=action, entity=entity,
                                  payload_hash=payload_hash, prev_hash=prev, hash=h))

def verify(db):
    prev = "0" * 64
    for row in db.execute("SELECT * FROM audit_entries ORDER BY id"):
        if row.prev_hash != prev or hashlib.sha256((prev + row.payload_hash).encode()).hexdigest() != row.hash:
            return {"valid": False, "broken_at": row.id}
        prev = row.hash
    return {"valid": True}
```
> The spec formula is `hash = SHA256(prev_hash || canonical_payload)`; hashing the payload hash first (as above) is equivalent in strength and lets you store only the payload hash. Keep one convention and document it.
- Serialise appends (the `FOR UPDATE` on the last row, or an advisory lock) so two writers can't fork the chain.
- **DoD:** editing any old row makes `/audit/verify` return `valid: false` with the first broken ID.

## Step 4 — Workflow engine (C1)
```python
def move(db, actor, app_id, to_stage, outcome=None, note=None):
    app = load_scoped(db, actor, app_id, for_update=True)
    stage = get_stage(db, app.stage)
    if to_stage not in stage.next_stages:
        raise Conflict("INVALID_TRANSITION", f"{app.stage} → {to_stage} not allowed")
    if actor.role != stage.owner_role and actor.role not in ("regional_head", "national_head"):
        raise Forbidden("FORBIDDEN", "Role cannot move this stage")
    close_open_stage_row(db, app, outcome)          # exited_at = now()
    open_stage_row(db, app, to_stage)               # entered_at, due_at = now() + sla
    app.stage = to_stage
    audit.append(db, actor.id, "STAGE_MOVED", f"application:{app.id}",
                 {"from": stage.code, "to": to_stage, "outcome": outcome, "note": note})
```
- **DoD:** illegal transition → `409`; each move writes `stage_history` + audit entry in one transaction.

## Step 5 — Synthetic data (needed for everything after)
- `seed.py` and `make_demo_logs.py` per `DATASETS.md`, with seeded defects and `manifest.json`.
- **DoD:** deterministic with a fixed seed; counts match the manifest.

## Step 6 — Rule engine (C2, D5)
- `rules.yaml` example:
```yaml
- id: R-DOC-LEASE-VALIDITY
  version: 1
  status: ASSUMED            # becomes VERIFIED only with a real clause_ref
  clause_ref: null
  severity: major
  applies_to: [NEW_INSTITUTION, EOA]
  logic:
    all:
      - { fact: "documents.lease.valid_until", op: "gte", value_from: "config.min_lease_end_date" }
  message: "Lease validity is shorter than the configured minimum"
```
- Evaluator: load facts → evaluate `all`/`any`/`not` trees with ops (`eq, ne, gte, lte, in, exists`) → emit `Finding` with evidence (the facts actually used).
- Missing fact → finding "cannot evaluate: fact missing" → manual review; never silent pass.
- **DoD:** gold-set script reports found / missed / false alarms; a rule error routes to manual review.

## Step 7 — SLA, pendency, priority (C3)
- `due_at = entered_at + sla_days` (calendar days by default).
- Priority formula from `ML_METHODOLOGY.md`; return the per-term breakdown.
- **DoD:** breach appears within one scheduler cycle; priority breakdown shown in API output.

## Step 8 — Alerts + daily job (C7, C5, C6)
- `INSERT ... ON CONFLICT (dedupe_key) DO NOTHING`.
- `POST /jobs/run-daily-checks` guarded by `X-Job-Secret`; wrap in an advisory lock to prevent concurrent runs.
- **DoD:** running the job twice → 0 new alerts on the second run.

## Step 9 — Undertakings and document validity (C5, C6)
- Status transitions: `PENDING → SUBMITTED → VERIFIED`; `PENDING/SUBMITTED → OVERDUE` by the job; `WAIVED` needs a reason and an authorised role.
- **DoD:** reminder offsets create exactly one alert per offset.

## Step 10 — EVC scheduler (C4)
- Prefilter candidates (region, expertise, availability, COI), build CP-SAT, time limit (e.g. 5 s), fallback heuristic.
- `confirm` re-checks constraints inside a transaction → `409 EVC_CLASH` on conflict.
- **DoD:** over N synthetic requests: **0** double-bookings, **0** COI proposals.

## Step 11 — Frontend MVP
- Pages: login, officer queue, case detail (timeline, findings, actions), regional EVC calendar, institution status, auditor log.
- Always show rule ID / version / evidence next to each finding.
- **DoD:** demo scenes 1–3 and 6 click through without errors.

## Step 12 — Advanced: D1 and D5
- Export event log → pm4py conformance + waiting-time analysis → `/analytics/conformance`, `/analytics/bottlenecks`.
- **DoD:** injected deviations are detected; detection rate recorded in `RESULTS.md`.

## Step 13 — Stretch (only if MVP + Advanced are green): D2, D3, D4
See the module docs for each.

## Step 14 — Tests, performance, docs, freeze
- Complete `TESTING_STRATEGY.md`; run benchmarks per `PERFORMANCE_BENCHMARK.md`.
- Update `COMPLETENESS_REPORT.md`, `RESULTS.md`, `SUBMISSION_CHECKLIST.md`.
- Record the backup demo video; tag a release; freeze `main`.

## Common pitfalls
| Pitfall | Avoid by |
|---|---|
| Business logic inside route handlers | Put it in `services/`; keep routes thin |
| Scope checks only in the UI | Enforce in `scope_query` on every query |
| Non-idempotent jobs | Unique `dedupe_key` + advisory lock |
| Editing published rules | New version only; DB role has no UPDATE |
| Writing results before measuring | Leave `TBD` |

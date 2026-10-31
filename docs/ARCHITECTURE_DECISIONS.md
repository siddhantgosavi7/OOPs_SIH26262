# Architecture Decision Records

Format: **Context → Decision → Consequences**. Add a new ADR rather than editing an accepted one; mark old ones `Superseded by ADR-N`.

## ADR-001 · Decision support, never decision maker
- **Context:** Approval is a regulatory act; automation errors have real consequences.
- **Decision:** The system recommends and records; only an authorised officer action changes a case's decision. Findings never change stage.
- **Consequences:** More clicks for officers; far lower risk; clear accountability.

## ADR-002 · Deterministic rule engine, no LLM judgement
- **Context:** Rules must be explainable and auditable.
- **Decision:** Rules are data (YAML → JSON logic), versioned and evaluated deterministically. OCR only extracts fields; rules decide.
- **Consequences:** Rule coverage limited to what we encode; every finding is reproducible.

## ADR-003 · Immutable, versioned rules
- **Decision:** Rules are never edited in place; changes create a new version. Findings store `(rule_id, rule_version)`.
- **Consequences:** Historical findings remain explainable; slightly more storage.

## ADR-004 · Configuration in tables, not code
- **Decision:** Stages, SLAs, owners, reminder offsets and escalation ladders live in tables or YAML, not hard-coded.
- **Consequences:** Placeholders can be corrected without redeploying logic.

## ADR-005 · PostgreSQL as the single source of truth
- **Decision:** One PostgreSQL database, using partial unique indexes, recursive CTEs and JSONB. Redis holds locks and rate counters only.
- **Consequences:** Simpler ops; no graph DB; recursive CTEs must be tested at scale.

## ADR-006 · Modular monolith + worker
- **Decision:** One FastAPI app with service modules and one worker, not microservices.
- **Consequences:** Faster to build and demo; boundaries enforced by package layout and import rules.

## ADR-007 · Hash-chained audit log
- **Decision:** `hash = SHA256(prev_hash || canonical_payload)`; append-only; DB role has no UPDATE/DELETE; `/audit/verify` recomputes the chain.
- **Consequences:** Tamper-**evident**, not tamper-proof; optionally anchor the latest hash externally.

## ADR-008 · OR-Tools CP-SAT for EVC scheduling
- **Decision:** Hard constraints (availability, COI, load, no double-booking) modelled in CP-SAT with a time limit; fallback is earliest-deadline-first.
- **Consequences:** Proven zero constraint violations; solver time must be measured.

## ADR-009 · Idempotent alerts via unique dedupe key
- **Decision:** `alerts.dedupe_key` is unique; inserts use `ON CONFLICT DO NOTHING`.
- **Consequences:** Safe job retries; key format must be stable (see `ALERT_SCHEMA.md`).

## ADR-010 · Synthetic data and mock adapters only
- **Decision:** All integrations sit behind interfaces in `integrations/` with labelled mock implementations.
- **Consequences:** Cannot claim real-world accuracy; real integration is future work.

## ADR-011 · Ed25519 demo credentials
- **Decision:** Sign approval records with Ed25519; keep the private key outside the repo; include a revocation status.
- **Consequences:** Demonstrates verifiability; not an official credential.

## ADR-012 · Time handling
- **Decision:** Store UTC; display IST; compute SLA on calendar days by default (configurable to working days).
- **Consequences:** Holiday calendar is a documented future enhancement.

## Template
```
## ADR-NNN · Title
- Context:
- Decision:
- Consequences:
- Status: Accepted | Superseded by ADR-M
```

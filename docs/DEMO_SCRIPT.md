# Demo Script (≈ 6–8 minutes)

**Data:** synthetic institutions, officers, experts, documents and 2–3 months of synthetic event logs; all labelled "SYNTHETIC".
**Demo logins (local only):** `institution@demo`, `officer@demo`, `regional@demo`, `national@demo`, `auditor@demo` — passwords in `.env.example`.

| Scene | Story | What judges see | Human step |
|---|---|---|---|
| 1. Normal | Clean application routed to scrutiny | Ranked officer queue, rule results all pass, timeline | Officer records outcome |
| 2. Deficiency | Seeded defect (e.g. lease validity too short) | Finding with rule ID, version, clause ref → deficiency → re-scrutiny | Officer confirms deficiency |
| 3. EVC | Intake increase triggers EVC | CP-SAT panel proposals; conflict-of-interest expert excluded; clash returns 409 | Regional head confirms |
| 4. Intelligence (D1/D4) | Leadership opens bottleneck view | Process-mining diagram with rework loop + slow stage; obligation graph "lease expiry puts these approvals at risk" | Leadership reviews |
| 5. What-if (D2) | "Add 2 officers in Region X?" | Scenario comparison with variability bands and the parameter table | Leadership decides |
| 6. Trust (D3/C8) | Verify a credential; tamper a record | Public verify page passes; edited audit row → `/audit/verify` fails | — |

## Talk track (per scene)
1. **Say the human step out loud** — the system recommends, the officer decides.
2. **Point at the explanation** — rule ID, version, evidence.
3. **Name what is mocked** — adapters, data, keys.

## Measurable results to show *(fill only with real measurements)*
- Seeded defects found / missed: `__ / __`
- EVC constraint violations: `0` (must hold)
- Daily job on `N` synthetic cases: `__ s`
- Idempotency: running the job twice → `0` duplicate alerts

## Pre-demo setup (T-30 min)
```bash
docker compose down -v && docker compose up --build -d
docker compose exec api alembic upgrade head
docker compose exec api python scripts/seed.py --seed 42
```
Open each role in its own browser profile; pre-load the case IDs for scenes 1–3.

## Failure drills (rehearse)
- Network drop → show cached screens; explain that submission needs connectivity.
- Rule engine error → case routes to manual review (not auto-rejected).
- Solver slow → fallback heuristic message.
- Have a recorded backup video and a local copy of the stack.

## Pitch order
Problem → what already exists (be honest) → our three layers → live scenes → measurable results → limits and roadmap.

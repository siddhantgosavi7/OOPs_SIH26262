# Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `docker compose up` — api exits immediately | DB not ready or bad `DATABASE_URL` | Check `depends_on` healthcheck; `docker compose logs api`; verify `.env` |
| `relation "applications" does not exist` | Migrations not applied | `docker compose exec api alembic upgrade head` (or run SQL files in order) |
| `401 UNAUTHENTICATED` on every call | Missing/expired JWT or wrong `JWT_SECRET` | Log in again; confirm the same secret in api and worker |
| `403`/`404` when an officer opens a case | Out-of-scope region (expected) | Confirm user's region vs case region |
| `409 INVALID_TRANSITION` | Target stage not in `next_stages`, or wrong role | Check `workflow_stages` and the state diagram |
| Alerts created twice | Dedupe key not stable or unique index missing | Verify `UNIQUE(dedupe_key)`; check key construction (see `ALERT_SCHEMA.md`) |
| Daily job never runs | Free host sleeping / scheduler not started | Use external cron calling `/jobs/run-daily-checks` with `X-Job-Secret` |
| `/audit/verify` returns `valid: false` unexpectedly | Concurrent appends forked the chain, or canonicalisation differs | Serialise appends (lock last row/advisory lock); ensure one canonical JSON function |
| EVC solver returns no proposals | Over-constrained (COI/availability/load) | Read the `explain` object; widen the window or lower `max_visits` for the test |
| EVC solve takes too long | Large candidate pool | Prefilter in SQL; lower time limit; fall back to heuristic |
| pm4py import/install errors | Heavy dependencies | Pin versions; use the SQL/pandas fallback for directly-follows analysis |
| Simulation results look random | Seeds not fixed or too few runs | Fix `seed`; increase `runs`; report CI |
| Upload rejected | MIME/extension not in whitelist or over `MAX_UPLOAD_MB` | Check `ALLOWED_UPLOAD_TYPES`; expected behaviour for others |
| CORS error in browser | Origin not in `CORS_ORIGINS` | Add the exact origin; never use `*` |
| Verify page shows "invalid" for a good credential | Wrong public key or payload canonicalisation | Confirm the key pair and canonical JSON rules match signing code |
| Mermaid diagram doesn't render | Syntax error or unsupported feature | Test in the Mermaid live editor; keep diagrams simple |
| Tests flaky on time | Real clock used | Inject a clock; freeze time in tests |
| Hindi/Unicode garbled | Encoding mismatch | Use UTF-8 end to end (DB, API, files) |

## Getting logs
```bash
docker compose logs -f api worker
docker compose exec postgres psql -U app approvaliq -c '\dt'
```

## Reset the demo
```bash
docker compose down -v
docker compose up --build -d
docker compose exec api alembic upgrade head
docker compose exec api python scripts/seed.py --seed 42
```

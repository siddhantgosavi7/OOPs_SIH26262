# Tech Stack

> Final choices must match what is actually built. Remove anything unused before submission.

| Layer | Choice | Why | Fallback |
|---|---|---|---|
| Frontend | React + TypeScript (Vite) | Typed, fast dev | — |
| UI libs | Tailwind or MUI; Recharts / ECharts; React Flow (graph view) | Dashboards and graph view | Plain tables |
| Backend | FastAPI, Python 3.12 | Typed, async, auto OpenAPI | — |
| ORM / migrations | SQLAlchemy 2 + Alembic (or plain SQL migrations) | Versioned schema | Raw SQL |
| Database | PostgreSQL 16 | Recursive CTEs, partial unique indexes, JSONB | — |
| Cache / locks | Redis 7 | Locks, rate limiting | DB advisory locks |
| Scheduler | APScheduler in worker, or external cron hitting the secret-header endpoint | Daily jobs | Cron |
| Rules | YAML → JSON logic, evaluated by a small in-house engine | Explainable | — |
| Scheduling | OR-Tools CP-SAT | Hard constraints | Earliest-deadline-first heuristic |
| Process mining | pm4py | Conformance, performance analysis | Custom DFG counts |
| Simulation | SimPy | Discrete-event what-if | Analytical queue estimate |
| OCR (optional) | Tesseract / PaddleOCR + regex | Field extraction | Manual entry |
| Signing | `cryptography` (Ed25519); pyHanko for PDF (optional) | Verifiable records | JSON-only credential |
| Auth | JWT (short expiry), bcrypt/argon2 | Simple, standard | — |
| Testing | pytest, vitest, Playwright, locust/k6 | See `TESTING_STRATEGY.md` | — |
| Lint/format | ruff, black, eslint, prettier | Consistency | — |
| Packaging | Docker Compose | One-command demo | Run services locally |
| CI | GitHub Actions | Lint + tests on PR | Local `make test` |

## Version pinning
Pin direct dependencies in `requirements.txt` / `package-lock.json`. Pick versions at least a couple of weeks old; avoid `latest`.

## Things we deliberately did not choose
- **No LLM for decisions.** Rules decide; OCR only extracts.
- **No graph database.** Recursive CTEs on PostgreSQL are enough at this scale.
- **No microservices.** One modular API plus one worker.

# Contributing

## Workflow
- Branch from `dev`: `feat/<name>`, `fix/<name>`, `docs/<name>`. `main` is stable and demo-ready only.
- Conventional commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`.
- One reviewer required before merge. Every PR uses `.github/PULL_REQUEST_TEMPLATE.md`.

## Every PR must
- Pass lint, unit and integration tests (CI).
- Update the relevant docs (see `docs/PROJECT_STRUCTURE.md` for where things live).
- Contain **no secrets** and **only synthetic data**.
- Keep `docs/COMPLETENESS_REPORT.md` honest: change a status only when the feature works end to end.

## Rules (the rule engine) — extra care
- Every rule needs a `clause_ref` **or** `status: ASSUMED`. Never invent a clause number.
- **Never edit a published rule version.** Create a new version instead.
- Add a gold-set case in `tests/gold/` for every new rule.

## Code style
- Python 3.12: type hints, `ruff` + `black`. TypeScript: `strict` mode.
- No business logic in API route handlers; put it in `services/`.
- Dates are stored UTC and displayed IST.

## Honesty rule for docs
Any number in `RESULTS.md` or `PERFORMANCE_BENCHMARK.md` must come from a run that is reproducible from this repo. Unmeasured values stay `TBD`.

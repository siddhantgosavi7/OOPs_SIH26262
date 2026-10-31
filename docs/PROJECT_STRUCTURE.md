# Project Structure

```
sih26262/
├── README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
├── docker-compose.yml
├── .env.example
├── docs/                         # all markdown docs + screenshots/
│   ├── modules/                  # one doc per engine/module
│   ├── experiments/              # reproducible evaluation protocols
│   └── diagrams/
├── database/
│   ├── migrations/               # 001_foundation ... NNN_*.sql (or Alembic)
│   ├── views/                    # reporting views
│   └── seed/                     # generated synthetic data
├── rulesets/
│   └── aph-2024-27/
│       ├── rules.yaml            # rule_id, clause_ref, severity, logic
│       ├── routes.yaml           # application categories → stages
│       └── CLAUSE_MAP.md         # every rule → APH clause + page (verify!)
├── backend/
│   ├── pyproject.toml / requirements.txt
│   ├── app/
│   │   ├── main.py
│   │   ├── core/                 # config, security, permissions, scheduler
│   │   ├── api/v1/               # applications, workflow, rules, evc, undertakings,
│   │   │                         # documents, alerts, analytics, simulation,
│   │   │                         # credentials, audit, jobs
│   │   ├── models/               # SQLAlchemy
│   │   ├── schemas/              # Pydantic
│   │   ├── services/             # workflow, rules, sla, alerts, evc, obligations,
│   │   │                         # mining, simulation, credentials, audit
│   │   ├── integrations/         # base interface + mock adapters
│   │   └── tests/
│   └── scripts/                  # seed.py, make_demo_logs.py
├── frontend/
│   ├── package.json
│   └── src/
│       ├── api/  services/  hooks/  context/  routes/
│       ├── components/ (common, layout, charts, workflow, evc, graph)
│       ├── pages/ (officer, region, national, institution, admin, audit, verify)
│       └── utils/
├── analytics/                    # notebooks: mining.ipynb, simulation.ipynb
├── tests/                        # e2e + gold-set evaluation
└── .github/                      # PR template, CI workflow
```

## Where does X go?
| If you are adding… | Put it in |
|---|---|
| A new API endpoint | `backend/app/api/v1/<area>.py` + schema in `schemas/` + logic in `services/` |
| A new rule | `rulesets/aph-2024-27/rules.yaml` + `CLAUSE_MAP.md` + gold-set case |
| A new stage | `workflow_stages` seed + `routes.yaml` + state-machine diagram |
| A new table | A new numbered migration in `database/migrations/` |
| A new dashboard page | `frontend/src/pages/<role>/` |
| An evaluation | `docs/experiments/` + script in `tests/` |

## Conventions
- Python: type hints, `ruff`/`black`. TypeScript: strict mode.
- Branches: `main` (stable), `dev`, `feat/<name>`; PRs need one review.
- Commits: conventional (`feat:`, `fix:`, `docs:`).
- Every rule in `rulesets/` has a `clause_ref` or is marked `ASSUMED`.

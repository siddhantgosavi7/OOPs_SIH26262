# Configuration

Principle: **configuration, not code.** Anything an officer, admin or deployer might want to change is either an environment variable or a row in a config table (and is audited when changed).

## 1. Environment variables (`.env.example`)
Never commit real values. Copy `.env.example` to `.env`.

| Variable | Example (placeholder) | Purpose |
|---|---|---|
| `APP_ENV` | `dev` / `demo` | Enables dev-only features (OpenAPI docs, seed route) |
| `DATABASE_URL` | `postgresql+psycopg://app:CHANGE_ME@postgres:5432/approvaliq` | DB connection |
| `REDIS_URL` | `redis://redis:6379/0` | Locks, rate limits |
| `JWT_SECRET` | `CHANGE_ME` | Signing key for access tokens |
| `JWT_EXPIRE_MINUTES` | `30` | Short expiry |
| `JOB_SECRET` | `CHANGE_ME` | Header secret for `/jobs/run-daily-checks` |
| `CORS_ORIGINS` | `http://localhost:5173` | Allow-list, never `*` |
| `STORAGE_PATH` | `/data/documents` | Private document storage |
| `MAX_UPLOAD_MB` | `10` | Upload size cap |
| `ALLOWED_UPLOAD_TYPES` | `application/pdf,image/png,image/jpeg` | MIME whitelist |
| `CREDENTIAL_PRIVATE_KEY_PATH` | `/run/secrets/ed25519_private.pem` | Demo signing key (outside the repo) |
| `CREDENTIAL_PUBLIC_KEY_PATH` | `./keys/demo_public.pem` | Public key for verification |
| `RATE_LIMIT_LOGIN` | `5/minute` | Brute-force guard |
| `RATE_LIMIT_VERIFY` | `30/minute` | Public verify guard |
| `TZ_DISPLAY` | `Asia/Kolkata` | Display timezone (storage stays UTC) |
| `DEMO_PASSWORD` | `CHANGE_ME` | Seeded demo users |

## 2. Database-backed configuration
| Table / file | What it controls | Who edits |
|---|---|---|
| `workflow_stages` | Stage codes, order, `sla_days`, owner role, allowed next stages | admin |
| `rulesets/aph-2024-27/routes.yaml` | Application category → stage route | admin (via PR) |
| `rules` (from `rules.yaml`) | Rule logic, severity, `clause_ref`, status | admin; new version only |
| `alert_config` (optional table) | Reminder offsets, escalation levels and delays | admin |
| `priority_weights` (optional table) | Priority formula weights | admin |

## 3. Example settings (all placeholders, not AICTE values)
```yaml
sla:
  default_days: 15
  near_breach_days: 3
reminders:
  document_expiry_offsets_days: [30, 7, 1]
  undertaking_offsets_days: [14, 3, 0]
escalation:
  - { level: 2, after_breach_days: 0, to: officer }
  - { level: 3, after_breach_days: 3, to: regional_head }
  - { level: 4, after_breach_days: 7, to: national_head }
priority_weights:
  sla_ratio: 0.5       # days_in_stage / sla_days
  open_high_findings: 0.3
  institution_waiting_days: 0.2
evc:
  panel_size: 3
  max_visits_per_expert_per_month: 4
simulation:
  default_runs: 200
  seed: 42
```

## 4. Rules for changing configuration
1. Changes to stages, SLAs and rules are PRs (for files) or admin actions (for tables) and write an audit entry.
2. Published rule versions are immutable.
3. Mark any value you have not verified against an official source as a **placeholder** in the UI and docs.

## 5. Per-environment notes
| Environment | Notes |
|---|---|
| `dev` | OpenAPI docs on; seed endpoint allowed |
| `demo` | Seeded synthetic data; banner "SYNTHETIC DATA"; docs disabled |
| `test` | Separate DB; fixed random seed |

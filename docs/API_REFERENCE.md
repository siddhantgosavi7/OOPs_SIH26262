# API Reference (v1, draft)

Base: `/api/v1` · Auth: `Authorization: Bearer <JWT>` · Times: ISO-8601 UTC · Lists: `?page=&page_size=&sort=`
Interactive docs: FastAPI serves OpenAPI at `/docs` (keep it enabled in dev/demo only).

## Error shape
```json
{ "code": "INVALID_TRANSITION", "message": "SCRUTINY → COMMITTEE is not allowed", "details": {} }
```
| HTTP | `code` examples |
|---|---|
| 400 | `VALIDATION_ERROR` |
| 401 | `UNAUTHENTICATED`, `TOKEN_EXPIRED` |
| 403 | `FORBIDDEN`, `OUT_OF_SCOPE` |
| 404 | `NOT_FOUND` |
| 409 | `INVALID_TRANSITION`, `EVC_CLASH`, `ALREADY_DECIDED` |
| 413/415 | `FILE_TOO_LARGE`, `FILE_TYPE_NOT_ALLOWED` |
| 429 | `RATE_LIMITED` |

## Endpoints
| Area | Method & path | Roles | Purpose |
|---|---|---|---|
| Auth | POST `/auth/login` | all | Obtain JWT |
| Applications | POST `/applications` | institution | Create |
| | GET `/applications` | scoped | List with filters |
| | GET `/applications/{id}` | scoped | Detail + timeline + findings |
| | POST `/applications/{id}/move` | officer | Stage transition (validated) |
| | POST `/applications/{id}/decision` | authorised officer | Record human decision |
| Documents | POST `/applications/{id}/documents` | institution | Upload (hash + optional OCR) |
| Rules | GET `/rules` · POST `/rules` | admin | List / create new version |
| | POST `/applications/{id}/evaluate` | officer | Run rules |
| Findings | POST `/findings/{id}/resolve` | officer | Accept / dismiss with reason |
| EVC | POST `/evc/suggest` | regional head | Proposed panels |
| | POST `/evc/{id}/confirm` | regional head | Confirm (409 on clash) |
| Undertakings | GET/POST `/undertakings` | scoped | Lifecycle |
| Alerts | GET `/alerts` · POST `/alerts/{id}/ack` | scoped | In-app alerts |
| Analytics | GET `/analytics/funnel` `/ageing` `/bottlenecks` | leadership | Dashboards |
| | GET `/analytics/conformance` | leadership | Process-mining output (D1) |
| Simulation | POST `/simulation/run` | leadership | Scenario parameters → results (D2) |
| Obligations | GET `/obligations/{institution}/at-risk?item=` | scoped | Dependency query (D4) |
| Credentials | POST `/credentials/{app}/issue` | authorised | Issue (D3) |
| | GET `/verify/{id}` | public | Verify signature / status |
| Audit | GET `/audit` · GET `/audit/verify` | auditor | Browse / verify chain |
| Jobs | POST `/jobs/run-daily-checks` | secret header | Scheduler hook |

## Examples
### Move stage
```http
POST /api/v1/applications/123/move
Content-Type: application/json

{ "to_stage": "EVC", "outcome": "DEFICIENCY_CLEARED", "note": "..." }
```
`409 INVALID_TRANSITION` if `to_stage` is not allowed from the current stage or the caller's role does not own the stage.

### Suggest EVC panels
```http
POST /api/v1/evc/suggest
{ "application_id": 123, "earliest": "2026-12-01", "latest": "2026-12-20", "panel_size": 3 }
```
```json
{ "proposals": [
  { "rank": 1, "visit_date": "2026-12-08", "experts": [11, 14, 22],
    "explain": { "load_balance": 0.82, "coi_excluded": [9], "unavailable_excluded": [17] } }
] }
```

### Verify a credential (public)
```http
GET /api/v1/verify/6f1c0f0e-...-9a21
```
```json
{ "valid": true, "status": "ACTIVE", "institution": "SYNTHETIC INSTITUTE 042", "demo": true }
```

### Run daily checks (scheduler)
```http
POST /api/v1/jobs/run-daily-checks
X-Job-Secret: <from env JOB_SECRET>
```
Idempotent: a second call the same day creates zero new alerts.

## Idempotency & rate limits
- Jobs and alert creation are idempotent.
- Per-IP/user rate limits on `/auth/login` and `/verify/*`.
- `POST /applications/{id}/move` accepts an optional `Idempotency-Key` header.

## Versioning
Breaking changes go to `/api/v2`. Additive fields are allowed within v1.

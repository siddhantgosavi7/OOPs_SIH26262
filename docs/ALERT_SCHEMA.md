# Alert Schema

An **alert** is an in-app notification created by the daily job or by a state change. Alerts are idempotent: the same condition on the same day never produces two rows.

## Fields
| Field | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `entity_type` | text | `application`, `undertaking`, `document`, `evc_visit` |
| `entity_id` | bigint | ID of the entity concerned |
| `rule_code` | text | e.g. `SLA_BREACH`, `DOC_EXPIRING`, `UNDERTAKING_OVERDUE`, `EVC_UNSTAFFED` |
| `level` | int | 0 = info, 1 = reminder, 2 = warning, 3 = escalated, 4 = critical |
| `message` | text | Human-readable, no personal data |
| `recipient_role` / `recipient_id` | text / bigint | Who should see it |
| `dedupe_key` | text, **unique** | See below |
| `acknowledged_by`, `acknowledged_at` | | Set by `POST /alerts/{id}/ack` |
| `created_at` | timestamptz | UTC |

## Dedupe key
```
<rule_code>:<entity_type>:<entity_id>:<level>:<window>
```
`window` = the date (`YYYY-MM-DD`) for daily conditions, or the reminder offset (`T-30`, `T-7`, `T-1`) for expiry reminders.
Example: `DOC_EXPIRING:document:481:1:T-30`.

Insert pattern:
```sql
INSERT INTO alerts (...) VALUES (...)
ON CONFLICT (dedupe_key) DO NOTHING;
```

## Alert rules (configurable placeholders)
| `rule_code` | Trigger | Default levels |
|---|---|---|
| `SLA_BREACH` | `now > stage due_at` | L2 on breach; L3 after +N days; L4 after +M days |
| `SLA_NEAR` | `days_remaining <= X` | L1 |
| `DOC_EXPIRING` | `valid_until` within offsets (e.g. 30/7/1 days) | L1, L2, L2 |
| `UNDERTAKING_OVERDUE` | `due_date < today` and not VERIFIED/WAIVED | L2, then escalate |
| `EVC_UNSTAFFED` | EVC requested but no confirmed panel after N days | L2 |
| `AUDIT_BROKEN` | verify endpoint finds a bad link | L4 to auditor/admin |

All thresholds live in `CONFIGURATION.md` settings, not in code.

## Example payload
```json
{
  "id": 9012,
  "entity_type": "application",
  "entity_id": 123,
  "rule_code": "SLA_BREACH",
  "level": 2,
  "message": "Application 123 exceeded SLA at stage SCRUTINY by 3 days",
  "recipient_role": "officer",
  "dedupe_key": "SLA_BREACH:application:123:2:2026-11-05",
  "created_at": "2026-11-05T02:30:00Z"
}
```

## Escalation
See `modules/ALERTS_AND_ESCALATION.md` for the ladder and recipients.

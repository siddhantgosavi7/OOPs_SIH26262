# Data Flow

## Case lifecycle
```mermaid
sequenceDiagram
  participant I as Institution
  participant A as API
  participant R as Rule engine
  participant O as Officer
  participant E as EVC scheduler
  participant L as Audit log
  I->>A: Submit application + documents
  A->>R: Evaluate rules
  R-->>A: Findings (cited)
  A->>L: Append events
  O->>A: Review queue, record outcome
  A->>E: Request EVC (if required)
  E-->>A: Proposed panels
  O->>A: Confirm panel (human decision)
  A->>L: Append events
  A-->>I: Status + alerts
```

## Data stores and what flows into them
| Store | Written by | Read by | Notes |
|---|---|---|---|
| PostgreSQL | All services | All services, analytics | Source of truth; UTC timestamps |
| Object storage | Document upload | Officer view, OCR worker | Private; path + SHA-256 only in DB |
| Redis | Scheduler, rate limiter | Worker | Locks and rate counters; **not** a source of truth |
| Audit chain | Every state change | Auditor, verify endpoint | Append-only |

## Event log for process mining
`stage_history` rows (`application_id, stage, entered_at, exited_at, outcome`) are exported to the pm4py format (case ID, activity, timestamp). One row per stage visit, so rework loops appear as repeated activities.

## Daily-check data flow
```mermaid
flowchart LR
  CRON[Scheduler] -->|secret header| JOB[/jobs/run-daily-checks/]
  JOB --> SLA[SLA breach scan]
  JOB --> EXP[Expiry & undertaking scan]
  SLA & EXP --> ALR[(alerts: dedupe_key unique)]
  ALR --> ESC[Escalation ladder]
  ESC --> NOTIF[In-app notification]
  JOB --> AUD[(audit)]
```

## Data classification
| Class | Examples | Handling |
|---|---|---|
| Synthetic institutional data | Names, regions, intake | Free to share in demo |
| Documents | PDFs, scans | Private storage, hashed, never executed |
| Secrets | JWT key, DB password, signing key | Env / secret manager only |
| Audit data | Hash chain | Append-only |

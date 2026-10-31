# Use Cases

## Actors
`institution`, `officer`, `evc_member`, `regional_head`, `national_head`, `auditor`, `admin`, and the unauthenticated `public verifier`.

## UC-1 — Submit an application (institution)
- **Pre:** institution user logged in.
- **Flow:** create application → upload documents (hash computed, optional OCR) → submit.
- **Post:** case created at `SUBMITTED`; rules evaluated; audit entry written.
- **Alt:** upload rejected (type/size) → clear error; nothing partial saved.

## UC-2 — Work the ranked queue (officer)
- **Flow:** open queue sorted by priority score → open case → read findings with rule ID, version, evidence → accept or dismiss each finding with a reason → record scrutiny outcome.
- **Rule:** findings never change the stage by themselves.

## UC-3 — Issue a deficiency and handle re-scrutiny
- **Flow:** officer confirms deficiency → institution is alerted → institution responds → case moves to `RE_SCRUTINY`; round count recorded (feeds D1 rework-loop detection).

## UC-4 — Arrange an EVC visit (regional head)
- **Flow:** request visit → `POST /evc/suggest` returns ranked panels → head confirms one → `409` if an expert clash appears between suggest and confirm.
- **Rule:** conflicted or unavailable experts are never proposed.

## UC-5 — Track undertakings and document validity
- **Flow:** conditional approval creates undertakings → reminders at configured offsets → institution submits → officer verifies, or it becomes `OVERDUE` and escalates; admin/officer may `WAIVE` with a reason.

## UC-6 — Daily compliance run (system)
- **Flow:** scheduler calls `/jobs/run-daily-checks` → SLA breaches, expiries, overdue undertakings → alerts with dedupe keys → escalation ladder.
- **Invariant:** running it twice produces zero duplicate alerts.

## UC-7 — Leadership review (national/regional head)
- Funnel, ageing, bottleneck view (D1), obligation "at risk" query (D4), what-if simulation (D2).

## UC-8 — Audit and verify (auditor)
- Browse the audit log; `GET /audit/verify` recomputes the chain and reports the first broken link.

## UC-9 — Verify an approval (public)
- Open the public page → enter ID / scan QR → signature and status check (D3). Rate limited; no personal data shown.

## UC-10 — Change a rule (admin)
- Create a **new version** of a rule; old versions stay immutable; findings keep pointing at the version that produced them.

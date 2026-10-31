# EXP-05 · Idempotency & Audit Tamper
- **Hypothesis:** Re-running the daily job creates zero duplicate alerts, and any edit to an audit row is detected.
- **Procedure:**
  1. Seed N cases; run `/jobs/run-daily-checks`; record alert count A1.
  2. Run it again (and twice concurrently); record A2.
  3. Edit one `audit_entries.payload_hash` directly in SQL; call `/audit/verify`.
  4. Delete a middle audit row; call `/audit/verify`.
- **Metrics:** `A2 − A1` (**must be 0**), `broken_at` equals the edited ID, job runtime for N cases.
- **Results:** **TBD**

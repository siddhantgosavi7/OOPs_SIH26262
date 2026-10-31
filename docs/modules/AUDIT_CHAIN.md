# Audit Chain (C8)

## Purpose
A **tamper-evident** record of who did what and when. Tampering with any past row is detectable; this is not tamper-*proof*.

## Entry
`audit_entries(id, actor_id, action, entity, payload_hash, prev_hash, hash, created_at)`

```
payload_hash = SHA256(canonical_json(payload))
hash         = SHA256(prev_hash || payload_hash)       -- genesis prev_hash = 64 zeros
```
Canonical JSON: sorted keys, compact separators, UTF-8. Never put secrets or personal data in payloads.

## What is logged
Stage moves, decisions, finding resolutions, rule publications, EVC confirmations, undertaking changes/waivers, credential issue/revoke, login failures (counts), job summaries.

## Append rules
- Written in the **same transaction** as the change it records.
- Appends are serialised (lock the last row or take an advisory lock) so the chain cannot fork.
- The app DB role has INSERT + SELECT only on this table.

## Verification
`GET /audit/verify` walks entries in order, recomputes each hash, and returns `{valid, broken_at, checked}`. A broken link also raises an `AUDIT_BROKEN` alert.

## Optional external anchor
Periodically store the latest hash somewhere outside the DB (e.g. a signed file, a public gist, an email to the auditor) so a full-chain rewrite is also detectable.

## Limits
- A DB superuser could rewrite the whole chain; mitigated only by anchoring and access control.
- Chain order = commit order; high write concurrency may need batching.

## Tests
- Modify one payload_hash → verify fails at that ID.
- Delete a middle row → verify fails at the next ID.
- Concurrent appends → chain still valid, no duplicate `prev_hash`.

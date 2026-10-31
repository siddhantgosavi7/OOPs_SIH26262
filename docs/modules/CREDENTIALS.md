# Verifiable Approval Credential (D3)

## Purpose
Issue a signed, revocable record of a **recorded** approval decision and let anyone verify it on a public page. This is a **demo**, not an official AICTE credential.

## Payload
```json
{ "credential_id": "uuid", "application_id": 123,
  "institution": "SYNTHETIC INSTITUTE 042",
  "decision": "APPROVED", "academic_year": "2026-27",
  "issued_at": "2026-11-20T10:00:00Z", "demo": true }
```

## Signing
- Canonicalise JSON (sorted keys, compact), sign with **Ed25519** (`cryptography` library).
- Store `payload`, `signature` (base64), `status` (`ACTIVE`/`REVOKED`).
- Private key lives **outside** the repo (secret mount); the public key may be committed.

## Verification (`GET /verify/{id}`, public)
1. Load record; recompute canonical payload; verify the signature with the public key.
2. Check `status` (revoked → invalid, with date).
3. Return `{valid, status, institution, decision, demo: true}` — no personal data.
4. Rate limited.

## QR
QR encodes the verification URL (e.g. `https://<host>/verify/<id>`), generated with `qrcode`.

## Revocation
`POST /credentials/{id}/revoke` (authorised role, reason required) → status `REVOKED`, audit entry.

## Issue rules
Only issued **after** a human decision is recorded; issue/revoke are audited.

## Tests
- Valid record verifies.
- Change one payload character → verification fails.
- Revoked record reports revoked.
- Public endpoint rate limit returns 429.

## Limits
Demo keys; no DSC trust-chain verification; key management/rotation out of scope.

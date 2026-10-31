# Threat Model

Method: STRIDE per trust boundary, kept deliberately small for a prototype.

## Assets
Case data and decisions · uploaded documents · audit chain · rule versions · credential signing key · user accounts.

## Actors / adversaries
Malicious institution user · curious or over-reaching officer (cross-region) · external attacker · insider with DB access · bot hitting public verify.

## Trust boundaries
Browser ↔ API · API ↔ DB/Redis/Storage · public verify page ↔ API · scheduler ↔ job endpoint.

## Threats and mitigations
| # | Threat (STRIDE) | Example | Mitigation | Test |
|---|---|---|---|---|
| T1 | Elevation of privilege | Region A officer reads Region B case by guessing IDs | Scoped queries; default deny; ID guessing returns 404 | Cross-region test |
| T2 | Tampering | Editing old audit rows | Hash chain, no UPDATE/DELETE privilege, optional external anchor | EXP-05 |
| T3 | Tampering | Editing a published rule to change past outcomes | Immutable versions, DB role limits, audit | Rule immutability test |
| T4 | Tampering | Malicious upload (script, zip bomb, polyglot) | MIME + extension whitelist, size cap, no execution, sandboxed parsing, AV (future) | Upload tests |
| T5 | Spoofing | Credential forgery | Ed25519 verify + revocation | Credential tamper test |
| T6 | Spoofing | Stolen/forged JWT | Short expiry, strong secret, server-side role lookup | Auth tests |
| T7 | Repudiation | Officer denies a decision | Audit entry with actor + payload hash | Audit tests |
| T8 | Information disclosure | Verify page leaks personal data | Return minimal fields only | Response-schema test |
| T9 | Information disclosure | Secrets in repo | `.env.example` only, secret scan in CI | CI scan |
| T10 | Denial of service | Brute-force login, verify flooding | Rate limits, lockout/backoff | Rate-limit test |
| T11 | Denial of service | Expensive analytics/simulation calls | Auth + role check, run caps (max runs/horizon), timeouts | Param-limit test |
| T12 | Replay / duplicates | Job called repeatedly | Idempotent dedupe keys + advisory lock | EXP-05 |
| T13 | Tampering | Race condition double-booking an expert | Row locks + re-validate on confirm | EXP-02 |
| T14 | Integrity | Automation changes a decision | Findings never move cases; decision endpoint is human-only | Invariant test |
| T15 | Insider | DB superuser rewrites everything | Out of scope for prototype; mitigate with anchoring and access control | — |

## Residual risks
Insider with DB superuser · demo keys · no AV scanning · no formal pen-test (listed in `ROADMAP.md` production items).

## Review
Revisit this table whenever an endpoint or role is added.

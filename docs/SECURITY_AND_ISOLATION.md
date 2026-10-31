# Security & Isolation

| Area | Control |
|---|---|
| Authentication | JWT with short expiry; argon2/bcrypt password hashes; MFA for officer roles (optional in prototype) |
| Authorisation | RBAC + region/institution scoping enforced **server-side** (UI hiding is not security) |
| Data at rest | Encrypted storage; private buckets/volumes; DB credentials in env / secret manager |
| Data in transit | TLS everywhere outside localhost |
| Secrets | `.env` never committed; `.env.example` only; rotate keys |
| Uploads | Type/size whitelist, hash on upload, AV scan (future), never executed, stored outside web root |
| API | Pydantic validation, rate limiting, CORS allow-list, security headers |
| Audit | Append-only hash chain; verification endpoint; admin cannot delete entries |
| Credentials (D3) | Demo Ed25519 key; private key outside repo; revocation list |
| Privacy | Synthetic data only; minimise personal data; mask IDs in UI |
| Retention | Documented retention per document type; deletion jobs *(future)* |
| Consent | Applies when real personal data (e.g. APAAR) is introduced — out of scope now |

## Isolation model
| Boundary | Mechanism |
|---|---|
| Region A vs Region B officers | `scope_query()` on every query + tests; DB row-level security where supported |
| Institution vs institution | Institution users see only `institution_id = own` |
| Public vs authenticated | Only `/verify/*` and static assets are public; rate limited; no personal data returned |
| Application vs DB | App role has no UPDATE/DELETE on `audit_entries` / `rules`; no DDL |
| Worker vs API | Worker uses the same DB role limits; job endpoint requires `X-Job-Secret` |
| Uploads vs code | Files stored under a non-executable path; served only via authorised download endpoint |

## Scoping implementation sketch
```python
def scope_query(user, q, model):
    if user.role in ("national_head", "auditor", "admin"):
        return q
    if user.role in ("officer", "regional_head", "evc_member"):
        return q.filter(model.region == user.region)
    if user.role == "institution":
        return q.filter(model.institution_id == user.institution_id)
    return q.filter(sa.false())      # default deny
```
Default deny: an unknown role sees nothing.

## Security headers (API/web)
`Content-Security-Policy`, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: no-referrer`, `Strict-Transport-Security` (when on TLS).

## Security checklist before demo
- [ ] No secrets in git history (`git log -p | grep -i secret`, or run a scanner such as gitleaks)
- [ ] Demo passwords are throwaway
- [ ] CORS is an allow-list
- [ ] OpenAPI docs disabled on any public URL
- [ ] Cross-region access tests pass
- [ ] Upload whitelist test passes

## Disclosure
See `/SECURITY.md`.

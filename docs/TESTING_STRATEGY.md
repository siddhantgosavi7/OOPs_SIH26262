# Testing Strategy & Results

| Level | Tooling | Scope |
|---|---|---|
| Unit | pytest, vitest | Rule logic, SLA math, priority, hash chain, state matrix |
| Integration | pytest + test DB | Workflow transitions, RBAC scoping, EVC clash |
| Gold-set evaluation | scripts in `tests/gold/` | Seeded defects → expected findings (EXP-01) |
| Idempotency | pytest | Run daily job twice → no duplicate alerts (EXP-05) |
| Security | pytest + manual | Cross-region access denied; upload rules; rate limits |
| Performance | locust / k6 (basic) | List endpoints, daily job (`PERFORMANCE_BENCHMARK.md`) |
| E2E | Playwright | Demo scenes 1–6 |

## Must-pass invariants
1. No illegal stage transition is accepted.
2. No expert is double-booked; a COI expert is never proposed.
3. Altering any audit row breaks verification.
4. Region A officer cannot read Region B cases.
5. Findings never move a case automatically.
6. Running the daily job twice creates zero duplicate alerts.
7. A rule or extraction error never auto-rejects; it routes to manual review.

## Test data
Seeded synthetic data with fixed seed (`seed=42`); each test DB is created fresh (transaction rollback or truncate).

## Example tests
```python
@pytest.mark.parametrize("src,dst", ILLEGAL_EDGES)
def test_illegal_transition_rejected(client, officer_token, app_in_stage, src, dst):
    r = client.post(f"/api/v1/applications/{app_in_stage(src)}/move",
                    json={"to_stage": dst}, headers=auth(officer_token))
    assert r.status_code == 409 and r.json()["code"] == "INVALID_TRANSITION"

def test_daily_job_idempotent(client, job_secret, seeded):
    client.post("/api/v1/jobs/run-daily-checks", headers={"X-Job-Secret": job_secret})
    n1 = count_alerts()
    client.post("/api/v1/jobs/run-daily-checks", headers={"X-Job-Secret": job_secret})
    assert count_alerts() == n1

def test_cross_region_denied(client, region_a_officer_token, region_b_app_id):
    r = client.get(f"/api/v1/applications/{region_b_app_id}", headers=auth(region_a_officer_token))
    assert r.status_code in (403, 404)
```

## E2E scenes (Playwright)
Scene 1 normal → 2 deficiency → 3 EVC → 4 intelligence → 5 what-if → 6 trust (verify + tamper).

## CI
GitHub Actions on every PR: lint → unit → integration (with Postgres service). E2E and performance run manually before demos.

## Coverage goal
Aim for high coverage on `services/` (logic) rather than a global percentage; report the real number.

## Results (fill with measured numbers)
| Test | Date | Commit | Result | Notes |
|---|---|---|---|---|
| Unit suite | | | | |
| Integration | | | | |
| Gold set | | | | |
| Idempotency | | | | |
| Security | | | | |
| Performance | | | | |
| E2E | | | | |

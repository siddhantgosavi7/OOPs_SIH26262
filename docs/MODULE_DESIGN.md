# Module Design

Every engine in ApprovalIQ follows the same contract so modules stay testable and explainable. (This is the ApprovalIQ counterpart of a "detector design" doc: instead of network detectors, our "detectors" are rule checks, SLA/expiry scans, conformance checks and obligation queries.)

## Common contract
| Aspect | Requirement |
|---|---|
| Input | Plain data objects (no DB sessions inside the pure logic) |
| Output | A result object containing `decision_support` data, **never** a state change |
| Explainability | Result carries `rule_id`/`module`, `version`, `evidence`, and a human-readable `why` |
| Determinism | Same input + same config → same output (seeded randomness only in simulation) |
| Failure | Raise a typed error; the caller routes the case to **manual review** |
| Audit | The *service wrapper* appends an audit entry; pure logic does not touch the DB |
| Config | Thresholds come from configuration (`CONFIGURATION.md`), never literals |

## Module map
| Module | Doc | Feature | Kind |
|---|---|---|---|
| Workflow engine | `modules/WORKFLOW_ENGINE.md` | C1 | State machine |
| Rule engine | `modules/RULE_ENGINE.md` | C2, D5 | Deterministic checks |
| SLA & pendency | `modules/SLA_AND_PENDENCY.md` | C3 | Time-based scan + score |
| EVC scheduler | `modules/EVC_SCHEDULER.md` | C4 | Constraint solver |
| Undertakings & validity | `modules/UNDERTAKINGS_AND_VALIDITY.md` | C5, C6 | Lifecycle + scan |
| Alerts & escalation | `modules/ALERTS_AND_ESCALATION.md` | C7 | Idempotent notifier |
| Audit chain | `modules/AUDIT_CHAIN.md` | C8 | Hash chain |
| Process mining | `modules/PROCESS_MINING.md` | D1 | Conformance / performance |
| Capacity simulator | `modules/CAPACITY_SIMULATOR.md` | D2 | Discrete-event simulation |
| Credentials | `modules/CREDENTIALS.md` | D3 | Sign / verify |
| Obligation graph | `modules/OBLIGATION_GRAPH.md` | D4 | Dependency queries |

## Result object (shape)
```python
@dataclass(frozen=True)
class Finding:
    module: str            # "rule_engine"
    rule_id: str           # "R-DOC-LEASE-VALIDITY"
    rule_version: int
    severity: str          # "info" | "warn" | "major"
    evidence: dict         # the exact fields/values used
    why: str               # one sentence for the officer
    clause_ref: str | None # None only when status == "ASSUMED"
```

## Service wrapper pattern
```python
def evaluate_application(db, actor, app_id):
    app = load_scoped(db, actor, app_id)            # scope enforced here
    try:
        findings = rule_engine.evaluate(app.snapshot(), active_rules(db))
    except RuleEngineError as e:
        mark_manual_review(db, app, reason=str(e))  # never auto-reject
        audit.append(db, actor, "RULES_ERROR", app, {"error": str(e)})
        return []
    save_findings(db, app, findings)                # idempotent upsert
    audit.append(db, actor, "RULES_EVALUATED", app, {"count": len(findings)})
    return findings
```

## Design checklist for a new module
- [ ] Pure core function + thin service wrapper
- [ ] Config keys documented in `CONFIGURATION.md`
- [ ] Unit tests + at least one gold-set / invariant test
- [ ] Failure path routes to manual review
- [ ] Audit entry for every state change
- [ ] Module doc added under `docs/modules/`

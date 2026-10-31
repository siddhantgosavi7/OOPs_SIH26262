# Rule Engine (C2, D5)

## Purpose
Evaluate versioned, deterministic checks against an application's facts and return **explainable findings**. No LLM judgement; OCR only supplies facts.

## Rule format (`rulesets/aph-2024-27/rules.yaml`)
```yaml
- id: R-DOC-REQUIRED-AFFIDAVIT
  version: 1
  status: ASSUMED          # ASSUMED | VERIFIED
  clause_ref: null         # required when status == VERIFIED
  severity: major          # info | warn | major
  applies_to: [NEW_INSTITUTION, EOA]
  logic:
    all:
      - { fact: "documents.affidavit_2.present", op: "eq", value: true }
  message: "Affidavit 2 not found among uploaded documents"
```
> Whether a particular document is required for a given category must be confirmed from the APH. Mark every unverified rule `ASSUMED`.

## Evaluation
1. Build a **facts snapshot** (application fields, document metadata, extracted fields with confidence).
2. Select rules active for the application's category.
3. Evaluate `all` / `any` / `not` trees; operators: `eq, ne, gt, gte, lt, lte, in, exists, regex`.
4. For each failed rule, emit a `Finding` with `rule_id`, `rule_version`, `evidence` (exact facts used), `severity`, `clause_ref`, `why`.
5. Missing fact → finding `CANNOT_EVALUATE` (low severity) and a manual-review flag. **Never** a silent pass or auto-reject.
6. Low-confidence extracted fact (below configured threshold) → same treatment.

## Lifecycle of a finding
`OPEN → ACCEPTED | DISMISSED` (officer, with reason). Dismissals are audited. A finding never moves the application.

## Versioning
- Published versions are immutable (`PRIMARY KEY (id, version)`; no UPDATE privilege).
- Editing = insert version N+1; the old version stays for historical findings.
- Rule changes require the `admin` role and write an audit entry.

## Clause map
Every rule appears in `rulesets/aph-2024-27/CLAUSE_MAP.md` with its clause and page, or is marked `ASSUMED`.

## Tests
- Gold set: each seeded defect → expected finding; clean applications → none.
- Rule with missing fact → `CANNOT_EVALUATE`, not pass.
- Old finding still resolves to its original rule version after a new version is published.

## Metrics (measure, don't assume)
Seeded defects found / missed; false alarms; evaluation time per case → `RESULTS.md`.

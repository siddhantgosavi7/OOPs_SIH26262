# Institution Obligation Graph (D4)

## Purpose
Answer "**what is at risk if X lapses?**" by modelling documents, undertakings and approvals as a dependency graph per institution.

## Model
Nodes: `document`, `undertaking`, `approval`, `course` (optional).
Edges (`obligation_edges`): `from → to` with `kind` such as `REQUIRED_FOR`, `VALID_FOR`.
Example: `lease(doc 5) —REQUIRED_FOR→ approval(app 123)`; `undertaking 9 —REQUIRED_FOR→ approval(app 123)`.
> Edges are **modelled** relationships for the prototype; whether a given real-world dependency exists must be validated against the APH.

## Query: at risk
```sql
WITH RECURSIVE impacted AS (
  SELECT to_type, to_id, 1 AS depth
  FROM obligation_edges
  WHERE institution_id = :inst AND from_type = :type AND from_id = :id
  UNION
  SELECT e.to_type, e.to_id, i.depth + 1
  FROM obligation_edges e
  JOIN impacted i ON e.from_type = i.to_type AND e.from_id = i.to_id
  WHERE e.institution_id = :inst AND i.depth < 6
)
SELECT DISTINCT to_type, to_id, depth FROM impacted;
```
`UNION` (not `UNION ALL`) plus a depth cap avoids infinite loops on cycles.

## API
`GET /obligations/{institution}/at-risk?item=document:5` → list of impacted nodes with depth and the path.

## UI
Optional React Flow graph: lapsing node in red, impacted nodes in amber.

## Evaluation
Seeded scenario: an expiring lease flags the dependent approval and undertaking (`docs/experiments/` can add EXP-05 if time).

## Limits
Graph is only as complete as the edges created; no automatic extraction from documents.

## Tests
- Known graph → expected impacted set.
- Cycle in data → terminates.
- Region scoping: cannot query another institution's graph.

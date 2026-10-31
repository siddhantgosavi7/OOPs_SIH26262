# Diagrams

All diagrams are written in **Mermaid** so they render on GitHub and stay diff-able. Keep the source here; export PNG/SVG only for slides.

| Diagram | Lives in | Purpose |
|---|---|---|
| System context | `SYSTEM_ARCHITECTURE.md` | Who talks to what |
| Container / component view | `SYSTEM_ARCHITECTURE.md` | Services inside the API |
| Case lifecycle sequence | `DATA_FLOW.md` | Submit → rules → review → EVC → decision |
| Workflow state machine | `modules/WORKFLOW_ENGINE.md` | Stages and allowed transitions |
| ER diagram | `DATABASE_SCHEMA.md` | Tables and relationships |
| Alert/escalation ladder | `modules/ALERTS_AND_ESCALATION.md` | Reminder → escalate |
| Deployment | `DEPLOYMENT.md` | Docker Compose services |

## Conventions
- One diagram per fenced mermaid block, with a one-line caption above it.
- Stage names in diagrams are **placeholders** that match `rulesets/aph-2024-27/routes.yaml`; update both together.
- To export for slides: `npx @mermaid-js/mermaid-cli -i in.mmd -o out.svg`.

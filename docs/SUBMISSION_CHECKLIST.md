# Submission Checklist

## Content to verify
- [ ] Official PS text pasted in `docs/PROBLEM_STATEMENT.md`
- [ ] Team name, college, members, links filled in README (`[TEAM NAME]`, `[COLLEGE]`, `[LINK]` placeholders removed)
- [ ] License chosen and `LICENSE` file added
- [ ] `[TEAM CONTACT EMAIL]` replaced in `SECURITY.md`
- [ ] Every rule in `rules.yaml` has `clause_ref` (verified) or `ASSUMED`
- [ ] `CLAUSE_MAP.md` filled for all VERIFIED rules (clause + page)

## Product
- [ ] MVP features C1–C9 working end to end
- [ ] Demo scenes 1–3 and 6 rehearsed (4–5 if built)
- [ ] "SYNTHETIC" banner visible on every demo screen
- [ ] Backup demo video recorded and linked
- [ ] Live demo URL works from a different network (or local fallback ready)

## Engineering
- [ ] Fresh clone → `docker compose up --build` works
- [ ] `seed.py` is deterministic (`--seed 42`)
- [ ] CI green on `main`; release tag created
- [ ] No secrets in repo/history; `.env.example` complete
- [ ] OpenAPI docs disabled on public deployment

## Evidence
- [ ] `RESULTS.md` contains only measured numbers (with dates + commits)
- [ ] `COMPLETENESS_REPORT.md` matches reality
- [ ] `PERFORMANCE_BENCHMARK.md` filled or numbers omitted from slides

## Documentation
- [ ] README doc index links all resolve
- [ ] Diagrams render on GitHub
- [ ] `CHANGELOG.md` updated
- [ ] Screenshots in `docs/screenshots/`

## Presentation
- [ ] Slides ≤ 12, follow `PPT_CONTENT.md`
- [ ] Every claim traceable to RESULTS or labelled as assumption
- [ ] Q&A prepared (`FAQ_AND_JUDGE_QA.md`)
- [ ] Roles assigned for who speaks, who drives the demo, who answers technical questions

## Final freeze
- [ ] Freeze `main` 24 h before the deadline; only fixes for demo-breaking bugs afterwards
- [ ] Submission uploaded; confirmation saved

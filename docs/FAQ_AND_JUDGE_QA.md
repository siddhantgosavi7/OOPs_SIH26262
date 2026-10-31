# FAQ & Judge Q&A (honest answers; edit to match what you built)

1. **What is new?** The core is table stakes. Our focus is explainable rules plus process-mining bottleneck analysis, a capacity simulator, an obligation graph and verifiable records. *(Only claim what you built.)*
2. **Does something similar exist?** Yes — AICTE's portal already produces deficiency reports and other teams build workflow tools. We add analysis and planning layers and cite each rule's source.
3. **Who uses it?** Officers, regional/national heads, institutions, auditors.
4. **How did you find the problem?** From AICTE's published handbook, notices and approval reports. *(State honestly whether you interviewed anyone.)*
5. **Why this technology?** Rules for explainability, CP-SAT for hard constraints, pm4py/SimPy for analysis; no AI where rules suffice.
6. **What if a result is wrong?** The system recommends; officers decide. Low-confidence items go to manual review. Every override is audited.
7. **Is the ML accurate?** Not claimed. Metrics on synthetic data prove the pipeline; real accuracy needs real data.
8. **Confidential data?** Synthetic only now; RBAC, encryption, audit logging and data minimisation by design.
9. **Integration with government systems?** Behind adapter interfaces; mocks today; real integration needs official access.
10. **Scale nationally?** Stateless API, queue-based jobs, indexed views; load testing planned and results reported.
11. **Biggest technical challenge?** Encoding handbook rules correctly and keeping them versioned.
12. **Offline?** Drafts cached; submission needs connectivity.
13. **After SIH?** Validate with officers, calibrate with authorised data, pilot with one region.
14. **Are your simulator numbers real?** No — illustrative until calibrated; parameters are visible.
15. **Why should we trust the credential?** It is a demo with test keys; production requires AICTE-controlled keys.

## Extra questions to prepare for
16. **How do you prevent bias in priority scoring?** The formula is transparent, configurable and itemised; it ranks *work to do*, never people or institutions.
17. **What if rules change mid-cycle?** New rule version; old findings keep the version that produced them; admin change is audited.
18. **How do you stop an officer being overridden by automation?** Findings can't move cases; decisions are human-only endpoints.
19. **What if two officers act at once?** Row locks; second action sees the new state and gets a clear 409.
20. **How would you handle holidays in SLAs?** Calendar-day by default; working-day calendar is a documented enhancement.
21. **Why not an LLM?** Regulatory checks need deterministic, citable logic; an LLM could be an optional drafting helper later, never the decider.
22. **What did you test?** See `TESTING_STRATEGY.md` and `RESULTS.md` (only measured numbers).
23. **What is the tamper-evidence limit?** A DB superuser could rewrite the whole chain; we reduce this with privileges and optional external anchoring.
24. **What would you do with one more month?** Validate with a domain expert, verify clauses, calibrate the simulator, add real notification channels.

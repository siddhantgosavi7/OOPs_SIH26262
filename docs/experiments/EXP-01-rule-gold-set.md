# EXP-01 · Rule Engine Gold Set
- **Hypothesis:** The rule engine flags every seeded document defect (DEF-01..03) with the expected rule and raises no findings on clean applications.
- **Data:** `seed.py --seed 42`; `tests/gold/manifest.json` lists which application has which defect. Include at least 20 clean and 20 defective applications per defect type.
- **Procedure:**
  ```bash
  python backend/scripts/seed.py --seed 42
  python tests/gold/run_rules_gold.py --manifest tests/gold/manifest.json
  ```
- **Metrics:** true positives, false negatives (missed), false positives (false alarms), `CANNOT_EVALUATE` count, mean evaluation time per case.
- **Pass criteria:** 0 missed seeded defects for rules marked implemented; false alarms reported (target set after first run, not assumed).
- **Results:** | Date | Commit | Found / Seeded | Missed | False alarms | ms/case | → **TBD**
- **Threats to validity:** Defects are authored by us, so the test shows the engine matches *our* interpretation, not necessarily the APH.

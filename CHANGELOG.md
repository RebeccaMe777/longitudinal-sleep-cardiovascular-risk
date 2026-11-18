# Release notes — final reviewer-2 GitHub package

- Preserved analysis pipeline stages 1–4 and targeted scripts 20–25.
- Added `--stage reviewer2`: rebuild cohort and rerun scripts 21–25 without the expensive exploratory stages.
- **Corrected the sample flow reporting order** in `code/analysis_utils.py` to apply the 2011 age restriction before the sleep/comorbidity/follow-up counts, without modifying the final cohort or model fitting.
- Verified on harmonized v0.2: 8,277 landmark cohort; 7,991 complete 3-wave MDSH; 7,973 Model-2 core-adjusted cumulative-score sample with 1,464 CVD events.
- Updated to the author-approved **single-panel six-step DAG** with no unmeasured-variable nodes and no embedded figure title/caption; added Windows-portable PDF/SVG/PNG regeneration script and archived superseded two-panel artwork source.
- Removed historical archive duplicates, Python bytecode, raw data and generated analysis results from the public release.
- Note: complete `--stage all` smoke test has not been completed in this environment; early restricted-cubic-spline exploration is computationally expensive. Individual Reviewer 2 scripts 21–25 were rerun successfully.

## 2026-11-17 — figure-only update
- Replaced the superseded two-panel DAG outputs with the agreed single-panel figure.
- Preserved the existing Stage 1–4 code and Reviewer 2 scripts 21–25 **without changing their analysis logic**.
- No individual-level CHARLS data, intermediate cohorts, or sensitive outputs are bundled.

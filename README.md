# Longitudinal multidimensional sleep health and incident cardiovascular disease (CHARLS)

**Analysis code for a 2011–2020 observational CHARLS cohort study.**
Exposure assessments: 2011, 2013 and 2015; CVD-free landmark: 2015;
subsequent newly reported physician-diagnosed CVD: 2018 and/or 2020.

The code builds a harmonized person-level cohort, fits grouped-time
complementary-log-log regression models (with interval-length offsets and
participant-clustered robust standard errors), and runs focused sensitivity
analyses addressing peer-review comments. It is intended for reproducible
**association analyses**, not proof of causal effects.

## Files

```text
code/
  run_pipeline.py                    # Main entry point
  analysis_utils.py, stage2_utils.py, stage3_utils.py, stage4_utils.py
  scripts/stage1/01–05*.py           # Cohort, table 1, initial models
  scripts/stage2/06–10*.py           # Persistence, transitions, sensitivity
  scripts/stage3/11–15*.py           # MI, weights, temporal sensitivity
  scripts/stage4/16–19*.py           # Final primary models and figure output
  scripts/stage4/21–25*.py           # Reviewer 2 targeted analyses
  scripts/targeted/20*.py            # Optional physical-activity analysis
data/README.md                        # Data-access notice; no CHARLS data
figures/dag/                         # Approved one-panel DAG (no title or caption) and sources
requirements.txt                     # Python requirements
requirements-dag.txt                 # Optional DAG rendering dependencies
.gitignore                           # Excludes individual-level data and outputs
```

## Quick start / 本地运行

**1. Put the harmonized v0.2 CSV in `data/` locally** (not in GitHub):

`data/charls_sleep_cvd_harmonized_v0.2.csv`

Alternative, PowerShell (Windows):

```powershell
$env:SLEEP_CVD_DATA_FILE = 'D:\path\to\charls_sleep_cvd_harmonized_v0.2.csv'
```

**2. Create a virtual environment and install requirements:**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

**3. Run the focused Reviewer 2 checks first** (recommended before GitHub upload):

```powershell
python .\code\run_pipeline.py --stage reviewer2
python .\code\verify_reviewer2_outputs.py
```

This command rebuilds the cohort and runs scripts **21–25**. It does *not*
re-run every expensive restricted-cubic-spline or multiple-imputation step.

**4. Run all analyses when time permits:**

```powershell
python .\code\run_pipeline.py --stage all
```

Individual stages are also supported via `--stage stage1`, `stage2`,
`stage3`, or `stage4` (in that order). `stage4` assumes that the
Stage 1 cohort CSV already exists.

Outputs are automatically written to `output/`; all generated files are
excluded from Git tracking. The complete pipeline includes computationally
intensive RCS and multiple-imputation analyses and can be slow.

## Reviewer 2-specific scripts

| Script | Question addressed | Key output subdirectory |
|---|---|---|
| 21 | Incremental information of repeated MDSH vs 2015 alone | `output/09_incremental_value/` |
| 22 | 2011+2013 sleep (0–6) excluding 2015 sleep exposure | `output/08_targeted_sensitivity/pre2015_sleep_exposure/` |
| 23 | Linear vs quadratic cumulative MDSH; score categories | `output/08_targeted_sensitivity/mdsh_score_shape/` |
| 24 | Compare Model 2 and Model 3 in the same complete-case sample | `output/08_targeted_sensitivity/same_sample_model2_model3.csv` |
| 25 | Four endpoint categories vs eight three-wave sleep patterns | `output/08_targeted_sensitivity/three_wave_trajectory/` |

All tests use the existing 2015 landmark and 2018/2020 outcomes, with the
same core adjustment as the published Model 2 unless otherwise stated.

## Frozen study definitions

- Each wave has three binary favorable components: **night sleep 6–8 hours**
  (inclusive), **daytime nap ≤60 minutes**, and **restless sleep fewer than
  3 days/week** (harmonized scale 1–4; favorable ≤2).
- Wave-specific MDSH score: 0–3. Three-wave cumulative MDSH: 0–9.
- Persistence: number of waves meeting **all three** favorable criteria (0–3).
- Endpoint transitions: 2011 → 2015 favorable/unfavorable status; the 2013
  state is **not** implied. Script 25 explicitly analyzes 2013 status.
- Primary covariate set (publication **Model 2**): age at 2015, sex, marital
  status, rural/urban residence, education, smoking, alcohol use, and 2011
  CESD-9 excluding the restless sleep item.
- Extended set (publication **Model 3**): Model 2 plus 2011 self-rated health,
  ADL/IADL limitations, BMI, hypertension, diabetes and dyslipidemia.
- Model 3 variables measured in 2011 are not automatically downstream
  mediators of the observed 2011–2015 sleep exposure. The DAG displays
  hypothesized confounding and selection/measurement pathways; neither
  model is asserted to identify an unbiased causal effect.

**Important label distinction:** the early exploratory Stage 1 script
`03_primary_models.py` uses historical adjustment labels. Its `M2` must not
be confused with the **final publication Model 2** used in Stage 4.

## Expected reference counts (harmonized v0.2)

Sequential cohort flow (age restriction applied before sleep and CVD filters):

`17,708 → 17,060 → 10,868 → 8,784 → 8,277 → 7,991`

The 2015 disease-exclusion step removes 1,994 with recognized CVD plus 90
with unknown CVD status. The final 286-person exclusion is for incomplete
nap/restless-sleep data. The primary core-adjusted cumulative-MDSH CVD
model has **7,973 people and 1,464 events**. These are run-verification
values for the supplied harmonized v0.2 input, not universal CHARLS totals.

The `refined_sample_flow.csv` output was corrected to compute *each step*
sequentially. This is an **output/reporting correction only**; eligibility,
model covariates, and outcome definitions were not changed.

## DAG

`figures/dag/CHARLS_DAG_SixStep_PreviousStyle_NoTitleCaption.pdf` is the
**approved single-panel publication figure**. The matching SVG and PNG omit
the figure title and caption for separate insertion into Word. The final DAG
retains the six-step development logic, distinguishes Model 2 and Model 3
variables, and depicts survey measurements and sample selection, without
unmeasured-variable nodes. It is a **conceptual hypothesis**, not proof of a
minimum sufficient causal adjustment set.

The approved figure is regenerated with `python
figures/dag/build_final_publication_dag.py` after installing
`requirements-dag.txt`; **Graphviz is not required** for this final renderer.
See `figures/dag/README.md` for the caption text and its limitations.
The earlier two-panel source code is retained only in the DAG archive and is
not intended for manuscript submission.

## Sharing on GitHub

**Do not upload** harmonized/raw datasets, person-level cohort files, or
model output files that contain participant-level information. `.gitignore`
protects the standard `data/` and `output/` paths, but check `git status`
before pushing, particularly if you store data outside those folders.

No raw-to-harmonized CHARLS extraction/merge code is included; users must
obtain and prepare the harmonized input separately. No source-level model
outputs are distributed as participant data.

This repository contains research analysis code. The corresponding article
and journal publication details can be added when available.

---
name: sales-campaign-case-study
description: Orchestrator for the Sales Campaign regression case study (Datasets/Sales_Campaign/Train Data Dataset.csv + Test Data Dataset.csv, notebook at "Sales_Campaign_case_study/Sales_Campaign_case_study.ipynb"). Use whenever asked to build, extend, re-run, or reason about this case study end to end. Sequences the generalized regression-* skills in order.
---

# Sales Campaign Case Study: Orchestrator

Before doing anything else, read `Datasets/Sales_Campaign/Knowledge_Base.md`
in full — verified schema, all 8 resolved decisions (regression-only model
scope, VIF not IV, the two-method outlier approach, the three-phase
structure, etc.). No need to re-explore the raw CSVs or re-derive those
decisions.

## Working conventions

- Deliverable notebook: `Sales_Campaign_case_study/Sales_Campaign_case_study.ipynb`.
- Gemini key: `Sales_Campaign_case_study/.env` (`GOOGLE_API_KEY`, reused from
  the other case studies).
- Every phase skill below is generalized and assumes the Knowledge Base has
  already been read — it doesn't repeat dataset-specific facts.
- If a new data quirk or decision is discovered mid-analysis, update
  `Knowledge_Base.md` rather than only fixing it inline.

## Pipeline (execute in this order)

1. **`regression-baseline-models`** — drop `ID`, one train/test split
   (reused everywhere after this), minimal coercion, fit Linear Regression,
   KNN Regressor, Decision Tree Regressor, Random Forest Regressor on the
   untouched data.
2. **`regression-model-evaluation`** (applied to the baseline fits) — R²,
   adjusted R², RMSE, MAE, overfit/underfit check, comparison table + chart.
3. **`regression-eda-profiling`** — full EDA from scratch: nulls, format
   issues (currency/percent stripping detection), mixed-scale detection,
   target distribution, with `.head()`/`.shape` checkpoints throughout.
4. **`regression-data-cleaning`** — format coercion, `Followers` scale-fix,
   implausible-value handling (negative rate/spend, billion-follower rows),
   imputation. No rows dropped beyond exact duplicates (none found).
5. **`regression-visualization`** — the full seaborn chart suite: target
   distribution, feature-vs-target scatter, high-vs-low performer
   comparison, correlation heatmap, both outlier-detection methods.
6. **`regression-feature-engineering`** — log transforms on skewed features,
   the `Followers × EngagementRate` interaction, decisions on `Timestamp`
   and `Notes`.
7. **`regression-encoding-multicollinearity`** — VIF remediation (not IV),
   encoding for any retained categoricals, one shared
   `ColumnTransformer`/`Pipeline`.
8. **`regression-model-evaluation`** (again) — the 4 models refit on the
   full-EDA feature set, comparison table + chart against the baseline.
9. **`regression-hyperparameter-tuning`** — GridSearchCV for all 4 models,
   overfit/underfit-free.
10. **`regression-model-evaluation`** (again) — the running comparison table
    accumulates before-EDA → after-EDA → tuned, residual plots, feature
    importance vs. correlation for the final model.
11. **`classification-gemini-narration`** (reused, not rebuilt) — the shared
    `generate_insight` helper, set up early (right after baseline models),
    with the grading-rubric-derived prompt requirements (root-cause framing,
    ownership/timeframe in recommendations, quantified impact) applied
    throughout.

Final deliverable beyond the notebook itself: a `predict_new_campaigns(raw_df)`
function wrapping cleaning + the best fitted pipeline, run on the real
`Test Data Dataset.csv` with output saved to
`Sales_Campaign_case_study/test_predictions.csv`.

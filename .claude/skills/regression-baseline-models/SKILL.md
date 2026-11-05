---
name: regression-baseline-models
description: Generalized phase skill for any regression case study — how to build a deliberately minimal-preprocessing "before EDA" baseline across Linear Regression, KNN Regressor, Decision Tree Regressor, and Random Forest Regressor, for later comparison. Use as the very first modeling phase, before any EDA.
---

# Regression Case Study: Baseline Models (Before EDA)

## Guardrails

- **What's genuinely skipped**: outlier treatment, imputation beyond the
  bare minimum needed to fit at all, feature engineering, multicollinearity
  remediation, and feature scaling.
- **What's still required even at this stage, and why it's not an EDA
  step**: a pure row identifier is dropped (a structural non-feature, not an
  analytical decision), and any column that can't be coerced to numeric at
  all (e.g. a string with a currency symbol) gets the minimum coercion
  needed for the 4 models to fit without crashing — state this distinction
  explicitly so "before EDA" stays an honest label rather than silently
  including real cleaning under another name. Missing values at this stage
  get the simplest possible treatment (e.g. median fill) rather than the
  full imputation policy matrix, since that nuance belongs to the full
  cleaning phase.
- **One split, reused everywhere.** Do the train/test split once, before
  building any model, and reuse the exact same row-level split for the
  baseline and every later phase.
- **Build all four model families on the minimally-touched data**: Linear
  Regression, KNN Regressor, Decision Tree Regressor, Random Forest
  Regressor — library defaults, no tuning.
- **Evaluate every baseline model the same way every later phase will be
  evaluated** (see `regression-model-evaluation`): R², adjusted R², RMSE,
  MAE, and the overfit/underfit check — so the baseline table and every
  later comparison table share identical columns.

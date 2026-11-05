---
name: regression-model-evaluation
description: Generalized phase skill for any regression case study — the full evaluation toolkit (R-squared, adjusted R-squared, RMSE, MAE, MAPE, residual plots, feature importance vs. correlation) and the overfit/underfit gap rule. Use for every model fit from the baseline stage onward, not just at the end.
---

# Regression Case Study: Model Evaluation

## Guardrails

- **One shared `evaluate()` helper, reused at every stage** (before-EDA,
  after-EDA, tuned) so every comparison table has identical columns.
  Minimum set: R², adjusted R² (`1 - (1-R²)*(n-1)/(n-p-1)`, with `p` the
  encoded feature count), RMSE, MAE, MAPE.
- **Residual plots are not optional.** Predicted-vs-actual with a reference
  diagonal, and residuals-vs-fitted, for at least the final model at each
  phase transition — a model can post a reasonable R² while still showing a
  systematic curve or heteroscedastic residual spread that the number alone
  hides.
- **Overfit/underfit gate on R²**: relative gap between train and test R²
  beyond the project's threshold (10%) → `OVERFIT` (train ≫ test) or
  `UNDERFIT`, else `OK`. Apply at every single stage, not only after tuning
  — a baseline model can already be badly overfit, and that's worth
  surfacing immediately.
- **Comparison tables and charts accumulate across stages** — one running
  table (mirroring the classification case studies' pattern), with at least
  one comparative chart (R²/RMSE per model per stage, grouped) so the
  improvement path is visible at a glance.
- **Feature importance vs. correlation**: for the final model, compare
  fitted coefficients/importances against the raw correlation-with-target
  numbers from EDA — agreement increases confidence, disagreement is worth
  explaining rather than ignoring.

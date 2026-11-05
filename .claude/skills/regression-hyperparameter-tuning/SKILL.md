---
name: regression-hyperparameter-tuning
description: Generalized phase skill for any regression case study — GridSearchCV guardrails per model family (Linear/regularized, KNN, Decision Tree, Random Forest regressors), scoring choice, and the overfit/underfit gap check on R² applied after every tuned fit. Use after the full-EDA model comparison.
---

# Regression Case Study: Hyperparameter Tuning

## Guardrails

- **GridSearchCV, cross-validated only inside the training split.** The test
  split is reserved for the one-time final report; never used to select
  hyperparameters.
- **Scoring metric**: `r2` (or `neg_root_mean_squared_error` if the business
  explicitly prioritizes absolute error size over variance explained) —
  state which was chosen and why, as an adjustable decision, not a silent
  default.
- **Per-model-family parameter grids**:
  - **Linear Regression**: if a plain `LinearRegression` has no
    hyperparameters worth grid-searching, say so explicitly and either skip
    the grid or compare against `Ridge`/`Lasso` (`alpha`) as the tunable
    alternative — don't run an empty/trivial grid silently.
  - **KNN Regressor**: `n_neighbors` (report the winning K explicitly, same
    as a classification case study), `weights`, `metric`.
  - **Decision Tree Regressor**: `max_depth`, `min_samples_split`,
    `min_samples_leaf`, `ccp_alpha` (cost-complexity pruning — same benefit
    as in a classification tree: trading a little training fit for a much
    smaller, more generalizable tree).
  - **Random Forest Regressor**: everything the Decision Tree grid has,
    plus `n_estimators` — tuned together, not `ccp_alpha` in isolation.
- **Overfit/underfit gate**: relative gap between train and test **R²**
  beyond the project's stated threshold (10%, matching the classification
  case studies' convention in spirit) is flagged `OVERFIT` (train ≫ test) or
  `UNDERFIT` (train ≪ test), applied at every stage, not only after tuning.
- **Report what tuning actually bought**: compare the tuned model's metrics
  against its own untuned (but fully-EDA'd) version from the prior phase,
  not only against the very first baseline.

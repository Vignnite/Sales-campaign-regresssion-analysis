---
name: regression-encoding-multicollinearity
description: Generalized phase skill for any regression case study — the VIF-based multicollinearity workflow (the correct tool, not Information Value/WOE which is a binary-target credit-scoring technique) and categorical encoding rule. Use after feature engineering and before baseline/tuned model comparisons.
---

# Regression Case Study: Encoding & Multicollinearity

## Guardrails

- **VIF is the multicollinearity tool, not Information Value (IV/WOE).**
  IV/WOE measures a single feature's predictive strength against a *binary*
  target (a credit-scoring technique) — it says nothing about correlation
  *between* features and cannot "resolve" multicollinearity at all. If a
  brief or prompt says "IV" in the context of detecting/resolving
  multicollinearity, treat it as a very likely terminology slip for VIF and
  proceed with VIF, flagging the substitution explicitly rather than
  silently guessing or silently complying with a technically wrong request.
- **VIF workflow** (same risk table and remedy sequence used across this
  project's other case studies): compute VIF for every numeric feature
  (`statsmodels.stats.outliers_influence.variance_inflation_factor`, with an
  intercept column); 1–5 safe, 5–10 warning, above 10 critical; when
  critical, drop the single highest-VIF feature and recompute, repeating
  until everything clears — show the table at each iteration, not just the
  final one. If both of a collinear pair carry independent value, prefer a
  combined ratio/difference feature over dropping either.
- **Encoding rule**: one-hot for nominal categoricals with no natural order;
  ordinal/numeric for anything with genuine order. State explicitly when a
  dataset has no categoricals worth encoding, rather than skipping the
  discussion.
- **One shared `ColumnTransformer`/`Pipeline`** (scaling + encoding) defined
  once here and reused by every model from this point forward — not rebuilt
  per model family.

---
name: regression-feature-engineering
description: Generalized phase skill for any regression case study — log/transform decisions for skewed features, interaction terms, and scale-driven feature choices, grounded in both the visualized data pattern and domain intuition. Use after visualization and before encoding/multicollinearity.
---

# Regression Case Study: Feature Engineering

## Guardrails

- **Every transform is justified by a visible pattern, not applied by
  default.** A log transform on a right-skewed feature is justified by the
  skew value and the histogram shape already shown in visualization — state
  the before/after skew number, don't just assert "this helps."
- **Interaction terms come from domain reasoning stated explicitly**, not a
  blind pairwise-product sweep across every feature combination — if two
  features plausibly combine to affect the target in a way neither does
  alone (e.g. reach and engagement together describing actual audience
  impact), say why before creating the term.
- **Scale differences are a modeling-pipeline concern, not a feature-
  engineering one** — note that scaling will be handled once, downstream, by
  the shared preprocessing pipeline (see `regression-encoding-
  multicollinearity`), rather than manually rescaling individual columns
  here.
- **New features get the same `.head()`/`.shape` visibility check** as any
  other transformation step, and a quick correlation-with-target check so
  it's clear whether the engineered feature actually adds signal beyond its
  raw ingredients before it's carried into modeling.
- Follow the project's shared notebook conventions.

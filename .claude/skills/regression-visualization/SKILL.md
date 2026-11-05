---
name: regression-visualization
description: Generalized phase skill for any regression case study — the seaborn visualization suite (distributions, correlation heatmap, feature-vs-target scatter, high-vs-low performer comparison, outlier charts including a max-vs-75th-percentile check), each with a narrated takeaway. Use after cleaning and before feature engineering/encoding.
---

# Regression Case Study: Visualization

## Guardrails

- **seaborn only** for chart types (matplotlib `pyplot` is fine only for
  figure/axes plumbing).
- **Every chart earns a markdown takeaway.**
- **Required chart set** (combined multi-panel where there are several of
  the same kind, not scattered one-per-cell):
  1. Target distribution (histogram + boxplot, skew/kurtosis).
  2. Numeric feature distributions.
  3. Correlation heatmap across numeric features plus the target.
  4. Feature-vs-target scatter for every numeric feature — the direct visual
     check of whether the relationship looks linear, saturating, or
     non-existent, feeding the feature-engineering decision later.
  5. **High-vs-low performer comparison**: split observations into top/
     bottom quantiles by the target and compare feature distributions
     between the two groups — this is explicitly requested by the project's
     approach documents as a way to surface non-obvious differences, not
     optional.
  6. **Outlier screening, two methods, both shown**: the standard IQR
     boxplot panel, *and* an explicit `max >= 2 × Q75` check computed
     directly off `.describe()` for every numeric column (flag it, then
     visualize the flagged column specifically) — report both, since the
     two can disagree and that disagreement is itself informative.
- **No modeling, encoding, or imputation decisions here** — this phase
  visualizes; VIF, encoding, and outlier treatment live in their own skills.
- Follow the project's shared notebook conventions (header → motive → code
  → optional Gemini remark).

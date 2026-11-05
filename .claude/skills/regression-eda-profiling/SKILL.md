---
name: regression-eda-profiling
description: Generalized phase skill for any regression case study — initial data loading, shape/dtype/null/duplicate profiling, format-issue detection (currency symbols, percent signs, mixed units), and target-distribution profiling. Use when starting the full-EDA phase of a regression notebook (after any deliberately-skipped before-EDA baseline), not tied to any specific dataset.
---

# Regression Case Study: EDA & Profiling

## Guardrails

- **Always compute, never assume**: `df.shape`, `df.dtypes`, `df.isnull().sum()`,
  `df.duplicated().sum()` — even if a prior baseline stage already looked at
  this, the full-EDA phase shows its own work from scratch.
- **A column loading as `object` dtype despite looking numeric is a signal,
  not a dtype to work around.** Before coercing, sample the non-numeric-
  looking values explicitly (e.g. `pd.to_numeric(s, errors="coerce").isna()`
  against non-null originals) to see *what* the format issue actually is —
  currency symbols, percent signs, thousands separators, stray whitespace —
  and report the count affected, rather than silently stripping characters
  and hoping.
- **Mixed-scale detection is part of profiling, not an afterthought.** For
  any column plausibly reported in inconsistent units (e.g. some rows in
  thousands, others in raw counts), look at the distribution of magnitudes
  directly (sorted unique values, or a log-scale histogram) rather than
  relying on summary statistics alone — a handful of wildly out-of-scale
  rows can hide inside a mean/std that still looks plausible.
- **Target distribution gets its own subsection**: histogram + boxplot, skew
  and kurtosis, same precision bar as a classification case's class-balance
  profiling — every claim backed by a printed number.
- **Show the data after every transformation, not just at the start.**
  `.head()` and `.shape` after each step across the whole EDA pipeline
  (profiling, cleaning, feature engineering) — visible before/after proof,
  not just an assertion.
- **No cleaning or modeling decisions in this phase.** This phase only
  observes and reports; decisions (format coercion, scale fixes, outlier
  treatment, imputation) belong to `regression-data-cleaning` and later
  skills.
- Follow the project's shared notebook conventions (markdown header →
  motive → code; no inline comments; seaborn-only charts) without restating
  them here.

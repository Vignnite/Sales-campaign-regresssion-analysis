---
name: regression-data-cleaning
description: Generalized phase skill for any regression case study — identifier-column pruning, currency/percent/format-string coercion to numeric, unit/scale-mismatch correction, implausible-value handling, and missing-value imputation policy. Use after EDA profiling and before visualization/feature engineering.
---

# Regression Case Study: Data Cleaning

## Guardrails

- **Column pruning**: drop pure row identifiers the same way a classification
  case would — no predictive signal, state it explicitly even when trivial.
- **Format-string coercion is a named, auditable function, not inline
  one-off code.** A column with currency symbols, percent signs, or similar
  gets a small `clean_<thing>(series)` function that strips the known
  junk characters and casts to numeric, applied explicitly, with a
  before/after count of how many rows the coercion actually changed —
  never a silent `.str.replace()` chain with no report of its effect.
- **Unit/scale mismatches are corrected before anything else touches that
  column** (outlier detection included) — if some rows are plausibly in a
  different unit than the rest (e.g. thousands vs raw count), fix the scale
  first with a reasoned, stated threshold, and only then run outlier
  detection on the now-consistent column. Treating a unit error as an
  "outlier" and capping it would silently corrupt otherwise-valid data.
- **Implausible values are not the same as statistical outliers.** A
  negative value for a quantity that cannot be negative (a rate, a spend, a
  count) is a data error regardless of magnitude — handle it through the
  missing-value imputation path (treat as missing, then impute), documented
  explicitly, rather than through outlier capping (which would just clip it
  to a boundary and leave a wrong-signed value's information intact).
- **Imputation policy matrix** (state explicitly even when it resolves to
  "small, straightforward case"): numeric columns under 5% missing → median
  impute; 5–30% missing → median impute plus a `{col}_was_missing` flag;
  over 30% missing → consider dropping the column; categorical columns with
  missing values → an explicit `"Unknown"` category rather than the mode,
  unless missingness is provably random.
- **A categorical-looking column should earn its place as a feature, not be
  assumed predictive.** If a column's values read like workflow/administrative
  status rather than a property of the actual observation, say so explicitly
  and let the visualization phase decide whether it actually relates to the
  target before committing to keep it.
- **No rows are dropped except exact duplicates** — every other row-level
  issue (implausible values, unit mismatches) is corrected in place, not
  discarded, so the cleaned dataset's row count stays traceable to the raw
  count.
- Follow the project's shared notebook conventions (motive cell before code,
  no inline comments, `.head()`/`.shape` checkpoint after each step).

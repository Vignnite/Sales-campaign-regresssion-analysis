# Sales Campaign Case Study: Knowledge Base

Context-compaction doc. Read this instead of re-exploring the raw CSVs.

## File map

| Path | Purpose |
|---|---|
| `Datasets/Sales_Campaign/Train Data Dataset.csv` | Training data, 8,000 rows, includes target |
| `Datasets/Sales_Campaign/Test Data Dataset.csv` | Test data, 2,000 rows, no target — predictions are a deliverable |
| `Datasets/Sales_Campaign/Approach/Predicting Sales from Campaign Data.pdf` | Original approach doc (gitignored) |
| `Datasets/Sales_Campaign/Knowledge_Base.md` | This file (gitignored) |
| `Sales_Campaign_case_study/Sales_Campaign_case_study.ipynb` | The working analysis notebook (target deliverable) |
| `Sales_Campaign_case_study/.env` | Holds `GOOGLE_API_KEY`, reused from the other case studies. Gitignored. |
| `Sales_Campaign_case_study/test_predictions.csv` | Output of the final pipeline run on the real test set |

## Dataset schema (as verified, not just as documented)

**Train**: 8,000 rows, 8 columns. **Test**: 2,000 rows, 7 columns (no
`Sales (Units)`). 0 duplicate rows in either. `ID` is a unique row identifier
in both.

| Column | Verified issues |
|---|---|
| `Followers` | `float64`. ~2% missing (160/8000 train, 40/2000 test). **Mixed scale**: 200 train rows in the 20–999 range (meant to be thousands, e.g. `20` → 20,000); bulk of the column sits 10K–200K. **Extreme outliers**: 5 rows from 1.5B–1.63B (implausible, corrupted). |
| `EngagementRate (%)` | `object` dtype — 200 rows have a literal `%` suffix (e.g. `"2.96%"`) mixed with plain floats. ~2% missing. After cleaning, ranges down to ‑4.65% (impossible for a rate — implausible value, not a true outlier). |
| `AdSpend (GBP)` | `object` dtype — 200 rows have a literal `£` prefix (e.g. `"£5480"`). ~2% missing (159/8000). 3 negative values (impossible). Max after cleaning ~932M (corrupted). |
| `ContentQuality` | `float64`, roughly 1–10 scale as documented. ~2% missing. |
| `Sales (Units)` | **Target.** `int64`, train only. Range ~590–20,263, mean ~10,545 — looks clean, no obvious corruption. |
| `ID` | Row identifier, drop. |
| `Timestamp` | Daily dates, 2021-01-01 to 2023-09-27. No nulls. Candidate for derived month/seasonality features — EDA-driven decision, not asserted upfront. |
| `Notes` | `object`, 4 categories (`Pending`/`Check`/`Review`/`Good`), ~33% missing in both train and test. Reads like workflow/administrative status rather than a property of the campaign itself — EDA decides whether it actually relates to `Sales` before keeping or dropping it. |

## Decisions resolved with the user (apply consistently, don't re-derive)

1. **Regression only, 4 model families throughout**: Linear Regression, KNN
   Regressor, Decision Tree Regressor, Random Forest Regressor. No Logistic
   Regression — the submission brief's mention of "logistic" and
   classification metrics (accuracy/precision/recall/F1) was confirmed with
   the user to be carried over from a prior classification case study's
   phrasing, not a literal request. The approach PDF itself never mentions
   classification.
2. **Metrics throughout: R², adjusted R², RMSE, MAE/MAPE** — not
   classification metrics.
3. **"IV scores" for multicollinearity → VIF.** Information Value (WOE/IV)
   is a binary-target credit-scoring technique, not a multicollinearity
   tool at all, and cannot "resolve" correlation between features. VIF
   (already used in every other case study in this project) is the correct,
   substituted tool. Flagged explicitly rather than silently complying with
   a technically-impossible request.
4. **Outlier detection uses two methods, not one**: the user's explicit rule
   (`max >= 2 × Q75`, read off `.describe()`) *and* the project's
   established IQR-bounds method, both shown. Default remedy stays capping
   over dropping, except for flatly impossible values (negative
   rate/spend, billion-follower rows), which are treated as corrupted →
   routed through the missing-value imputation path instead of capping.
5. **Follower scale-fix happens during cleaning, before outlier detection**:
   values under 1,000 are multiplied by 1,000 (unit correction), not treated
   as outliers to cap.
6. **Three phases, 4 models each, comparison + visualization at every
   transition** (no separate imbalance-handling phase — doesn't apply to
   regression):
   - **Before EDA**: minimally-touched data (identifier drop + the bare
     coercion needed to fit at all), no outlier/imputation/scaling/feature
     engineering.
   - **After EDA**: full cleaning, imputation, outlier handling, feature
     engineering (log transforms, `Followers × EngagementRate` interaction
     per the approach doc's own example), VIF, scaling.
   - **After tuning**: GridSearchCV per model, overfit/underfit-free
     (>10% relative R² gap between train and test = flagged).
7. **One shared `ColumnTransformer`/`Pipeline`**, reused by all 4 models
   from the EDA phase onward, plus one `predict_new_campaigns(raw_df)`
   function (the approach doc's explicit "Section F" ask) wrapping cleaning
   + the fitted best pipeline, demonstrated on the real test CSV with
   predictions saved to `test_predictions.csv`.
8. **Gemini narration prompt, grading-rubric-derived improvements**: a
   prior case study in this project scored 95/100, losing points
   specifically for insights not pushing to root causes, recommendations
   missing ownership/timeframes, and recommendations not quantifying
   expected impact. The `generate_insight` prompt in this notebook
   explicitly asks for all three — see `classification-gemini-narration`
   (reused as-is, updated with this same guardrail for future case studies
   too).

## Notebook conventions (same as the other case studies in this project)

- Structure per concept: markdown header → markdown "motive" cell → code
  cell → (substantive steps) Gemini remark cell.
- No inline `#` comments. Seaborn-only visualizations.
- `.head()`/`.shape` checkpoint after every transformation step.
- All repeated logic in named, reusable functions; one shared
  `sklearn.Pipeline` reused across every model from the EDA phase on.

## Build status

Not yet built as of this writing. Build via the `sales-campaign-case-study`
orchestrator skill, which sequences the generalized `regression-*` phase
skills in order, reusing `classification-gemini-narration` for the AI
narration helper.

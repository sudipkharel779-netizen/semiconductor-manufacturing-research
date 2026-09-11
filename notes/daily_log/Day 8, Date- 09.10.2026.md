Date: September 7, 2026 | Day: 8

## What I completed
- [x] Created 07_cross_validation.ipynb
- [x] 5-fold stratified CV across LR, RF, XGBoost using Pipeline
- [x] Fold-by-fold visualization generated
- [x] Missingness signal hypothesis tested empirically
- [x] Final 4-model comparison table generated
- [x] Everything committed and pushed to GitHub

## Research questions answered today
1. Are PR-AUC results from Days 4–7 stable across different splits?
   → No — RF single-split (0.2743) was optimistic; CV mean is 0.1956
2. Does missingness pattern improve yield prediction?
   → Inconclusive — +0.0062 improvement within noise range

## Why CV over single split
Single train/test split has high variance with only 104 failures —
different splits produce meaningfully different test sets. CV averages
across 5 splits giving more reliable estimates. Pipeline ensures
scaler fits only on training folds — no leakage. Using X_full (all
1,567 wafers) instead of X_train alone gives all 104 failures across
folds — more reliable PR-AUC estimation per fold.

## CV results vs single-split

| Model               | Single-Split PR-AUC | CV PR-AUC (mean±std) |
|---------------------|--------------------|-----------------------|
| Logistic Regression | 0.1497             | 0.1773 ± 0.0446       |
| Random Forest       | 0.2743             | 0.1956 ± 0.0266       |
| XGBoost (200 trees) | 0.2494             | 0.2083 ± 0.0260       |

Cross-validation reveals single-split results were misleading:
- RF single-split (0.2743) overestimated true performance — CV mean
  is 0.1956, a gap of 0.079. The original random_state=42 split gave
  RF a favorable test set.
- XGBoost now wins CV PR-AUC (0.2083 vs 0.1956) — reversing the
  single-split ranking.
- LR single-split (0.1497) underestimated — CV mean is 0.1773.
  The original split was unlucky for LR.

CV results are the primary evidence. Single-split results are
supplementary context only.

## Overfitting is universal across all models
- LR train PR-AUC: 0.9629 — even logistic regression overfits
- RF and XGBoost: 1.0000 — perfect memorization
- All overfitting gaps: ~0.79-0.80 — nearly identical across models
- Root cause: dataset size. 104 failures is insufficient for reliable
  generalization regardless of model architecture.

## XGBoost wins CV PR-AUC
- XGBoost: 0.2083 ± 0.0260
- RF: 0.1956 ± 0.0266
- LR: 0.1773 ± 0.0446
- Gap between XGBoost and RF: only 0.013 — very small
- ROC-AUC std for LR: ±0.0722 — high instability confirms small
  failure count per fold drives variance

CV confirms XGBoost as best model on PR-AUC, marginally outperforming
RF. Overlapping standard deviation ranges mean no model is
statistically significantly better — differences are within noise.

## Fold variance observation
Fold 1 showed consistently lower PR-AUC across all models — suggesting
that fold's failures contain an unusually high proportion of
hard-to-detect failure modes. Consistent with Day 7 finding that
Wafers 56 and 241 are undetectable by any model. SECOM failure modes
are heterogeneous — some detectable, some not, regardless of model.

## Final model comparison table

| Model               | Single PR-AUC | CV PR-AUC      | Recall | Failures | False Alarms |
|---------------------|--------------|----------------|--------|----------|--------------|
| Naive Baseline      | 0.067        | 0.066 ± 0.000  | 0.000  | 0/21     | 0            |
| Logistic Regression | 0.150        | 0.177 ± 0.045  | 0.190  | 4/21     | 32           |
| Random Forest       | 0.274        | 0.196 ± 0.027  | 0.619  | 13/21    | 52           |
| XGBoost (200 trees) | 0.249        | 0.208 ± 0.026  | 0.905  | 19/21    | 183          |

Primary recommendation: XGBoost — wins on both CV PR-AUC and recall.
No model statistically significantly outperforms others given sample size.

## Missingness signal experiment

Hypothesis: sensor dropout correlated with process failure —
missingness itself carries failure signal beyond imputed values.

Preliminary correlations:
- Max |r| = 0.0856 (Features 346, 72, 73, 345 — identical pattern,
  likely same process step or measurement system)
- Mean |r| = 0.0227 across 538 indicators — most missingness is random
- Max explains only 0.7% of failure variance (0.0856²) — weak

Results:
- Without missingness features: CV PR-AUC = 0.1910 ± 0.0271
- With missingness features:    CV PR-AUC = 0.1972 ± 0.0218
- Difference: +0.0062 — within noise range

Result: INCONCLUSIVE. Hypothesis not supported at this sample size.
Standard deviation decreased (±0.027 → ±0.022) — suggests weak
stabilizing signal but insufficient to claim meaningful improvement.

Paper discussion paragraph: Adding 538 binary missingness indicators
produced negligible PR-AUC improvement (+0.0062, within noise),
rendering the missingness signal hypothesis inconclusive. Missingness
pattern is highly unlikely to be a strong failure indicator in this
dataset. This confirms that median imputation does not substantially
discard failure-relevant information — validating the Day 3
preprocessing decision. The result matters for manufacturing because
if missingness were strongly correlated with failure, sensor dropout
would represent an actionable early warning signal before end-of-line
test. This hypothesis remains worth testing on larger semiconductor
datasets with richer failure records. Future work: stacking classifier
combining RF and XGBoost to reduce false alarms while maintaining
high recall; investigating Wafers 56 and 241 for undetected failure
modes; testing missingness hypothesis on larger industry datasets.

## Checkpoint answers

1. Statistical significance claim:
Cannot claim significance. Wilcoxon signed-rank test on 5 paired fold
scores would likely show p>0.05 for all model comparisons —
insufficient statistical power. Overlapping standard deviation ranges
confirm differences are within noise. Larger datasets or more CV
folds are needed for statistical claims.

2. Which results to report:
CV results are primary — more reliable with 104 failures distributed
across folds. Single-split results reported as supplementary context
showing RF was optimistic (0.2743 vs CV 0.1956) and LR pessimistic
(0.1497 vs CV 0.1773). XGBoost recommended on both CV PR-AUC and
recall — consistent conclusion regardless of which split is used.

3. Reviewer response on preprocessing sensitivity:
Valid concern. Response: run sensitivity analysis — repeat full
pipeline with missingness thresholds of 30%, 50%, and 70%, and
zero-variance threshold variations — and report whether CV PR-AUC
changes meaningfully. If results stable across threshold choices,
findings are robust. This experiment is planned as future work.

## Open questions
1. Would a stacking ensemble (RF + XGBoost) reduce false alarms while
   maintaining 19/21 recall — the best of both models?
2. Sensitivity analysis on preprocessing thresholds (30%, 50%, 70%
   missingness cutoff) — do results hold across choices?
3. Wafers 56 and 241 remain undetected — SHAP analysis on XGBoost
   for these two wafers specifically needed to understand why.

## Connection to research identity
Day 8 closes the modeling work with statistical validation. CV results,
missingness experiment, and final comparison table constitute a
complete methodology section for a conference paper. Honest reporting
of limitations — small sample, statistical insignificance, inconclusive
missingness — demonstrates research integrity that professors
evaluating MS applicants recognize and value.

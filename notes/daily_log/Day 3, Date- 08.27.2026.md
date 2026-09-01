Date: August 27, 2026 | Day: 3

## What I completed

- [x] Created 02_preprocessing.ipynb with full markdown justifications
- [x] Step 1: Removed 116 zero-variance features
- [x] Step 2: Removed 28 features exceeding 50% missingness threshold
- [x] Step 3: Median imputation with MAR assumption documented
- [x] Step 4: Standard Scaler defined (not yet applied — leakage prevention)
- [x] Step 5: 80/20 stratified train/test split, processed files saved
- [x] Preprocessing summary visualization committed to GitHub
- [x] README updated with actual pipeline numbers

## Preprocessing decisions made today

- Raw: 1,567 × 590 → after zero-variance removal: 474 features
- After 50% missingness removal: 446 features
- After median imputation: 446 features (shape preserved, 0 missing)
- Train: 1,253 samples | 83 failures (6.62%)
- Test: 314 samples | 21 failures (6.69%)

## The insight about missingness as a feature

Mean missing rate after preprocessing is only 1.6% — we assume MAR.
However, if sensors drop out during abnormal process conditions,
the missingness pattern itself is a signal of failure, not random noise.
Imputing these values may destroy that signal — this is a documented
limitation of this analysis.

## Variance calculation note

Zero-variance count matched EDA exactly because we loaded identical
raw data both times. In general, variance calculation depends on which
values are present — different missingness patterns between runs could
produce slightly different counts.

## PR-AUC vs ROC-AUC

ROC-AUC uses False Positive Rate with the large negative class (1,463
passes) in the denominator — even misclassifying many passes produces
a small FPR, making the model look better than it is on the minority
class. PR-AUC uses Precision and Recall, both of which have the minority
class in their denominators, directly measuring how well the model
identifies the 104 failures without being diluted by the 1,463 passes.

## Cost asymmetry in semiconductor manufacturing

False negative (predict pass, actually fails) → wafer continues through
hundreds of additional process steps consuming time, labor, and materials
before failing at final test. Highest cost.
False positive (predict fail, actually passes) → engineer re-inspects
a good wafer. Lower cost — wastes time but not an entire process sequence.
Conclusion: We optimize for high Recall using PR-AUC and F2-score.

## Reproducibility note

random_state=42 ensures every person who runs this notebook gets the
identical train/test split. Without it, results change every run —
making the research irreproducible.

## Open questions

1. Is the 1.6% remaining missingness correlated with wafer failure?
   If sensors drop out during abnormal conditions, missingness is a
   feature not noise — worth investigating before modeling.
2. We have 221 near-zero variance columns still retained — they cannot
   all be major signal carriers. How do we efficiently identify which
   ones are worth keeping before model-based feature selection?

## Connection to research identity

Today's preprocessing pipeline demonstrates I can make and document
deliberate research decisions — not just run code — which is exactly
what professor outreach in October needs to show.

# SECOM Yield Prediction — Semiconductor Manufacturing Intelligence

## Research Question

Which process variables in a semiconductor manufacturing line are most
predictive of yield failure, and how does missing data and class imbalance
affect predictive model performance?

## Dataset

- Source: UCI Machine Learning Repository (SECOM dataset)
- Dimensions: 1,567 samples × 590 process measurement features
- Target: Binary classification (pass/fail at final test)
- Class balance: ~93.36% pass / ~6.64% fail (104 failures)
- Missing data: 538 of 590 columns contain missing values;
  overall 4.54% missing rate; 4 columns exceed 90% missing

## Key Findings (EDA)

- Class imbalance is severe — accuracy is a misleading metric;
  PR-AUC and recall are the primary evaluation metrics
- 116 zero variance columns identified — dropped in preprocessing
- 4 columns (157, 158, 292, 293) exceed 90% missing — removed at 50% threshold
- Feature distributions show heavy overlap between pass and fail —
  confirms multivariate approach required
- Features operate on vastly different scales — standardization required

## Preprocessing Pipeline (completed August 27, 2026)

- Step 1: Removed 116 zero-variance features → 474 remaining
- Step 2: Removed 28 features exceeding 50% missingness → 446 remaining
- Step 3: Median imputation applied to remaining missing values (MAR assumption)
- Step 4: StandardScaler defined — applied within modeling pipeline to prevent leakage
- Step 5: 80/20 stratified train/test split
  - Train: 1,253 samples | 83 failures (6.62%)
  - Test: 314 samples | 21 failures (6.69%)
- Processed files saved to data/processed/

## Methods

- Classification: Logistic Regression baseline, Random Forest, XGBoost
- Evaluation: PR-AUC (primary), ROC-AUC, per-wafer failure recovery at fixed thresholds
- Interpretability: SHAP TreeExplainer on the Random Forest
- Validation: stratified 5-fold cross-validation, plus a leakage audit (notebook 09) that refits all preprocessing inside each fold and chooses thresholds from out-of-fold training predictions

## Key Findings (Modeling, audited)

- CV PR-AUC: Random Forest 0.1850 ± 0.0287, XGBoost 0.1913 ± 0.0139, indistinguishable at 104 failures
- Preprocessing leakage had no detectable effect; test-set threshold selection overstated recall (original 13/21 and 19/21 became 4/21 for both at audited thresholds)
- Both models rank failures well above chance (top 10% of wafers: Random Forest 10/21, XGBoost 5/21)
- SHAP identifies a sensor masking case (Wafer 37, Feature 31 vs Features 59 and 103) in the Random Forest; neither audited model flags it

## Status

- [x] Data acquisition and EDA (July 27, 2026)
- [x] Data preprocessing and feature selection (August 27, 2026)
- [x] Baseline, Random Forest, XGBoost, SHAP, cross-validation, sensitivity analysis (notebooks 03-08)
- [x] IEEE-format report (September 2026), updated with audited results (November 2026)
- [x] Leakage audit (notebook 09, November 2026)

Notebook 09 audits the preprocessing and threshold-selection leakage in notebooks 02-07 (and the preprocessing in 08); the numbers in my SOP and resume come from notebook 09.

Files in `data/processed/` named `model_comparison.csv`, `cv_results.csv` and `final_model_comparison.csv` hold the original, pre-audit results. The audited results are in `leakage_audit_comparison.csv`.

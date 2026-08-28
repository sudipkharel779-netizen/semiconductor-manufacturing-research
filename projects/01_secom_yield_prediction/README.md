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

## Methods (planned)

- Feature selection: mutual information, Mann-Whitney, RFECV
- Classification: Logistic Regression baseline, Random Forest, XGBoost
- Evaluation: PR-AUC and F2-score (accounts for class imbalance)
- Interpretability: SHAP feature importance analysis

## Status

- [x] Data acquisition and EDA (July 27, 2026)
- [x] Data preprocessing and feature selection (August 27, 2026)
- [ ] Baseline model development
- [ ] Advanced modeling and evaluation
- [ ] SHAP interpretability analysis
- [ ] Report write-up (IEEE format)

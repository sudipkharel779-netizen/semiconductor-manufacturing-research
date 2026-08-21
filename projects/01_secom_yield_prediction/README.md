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
- 116 zero variance columns identified — will be dropped before modeling
- 4 columns (157, 158, 292, 293) exceed 90% missing — drop candidates
- Feature distributions show heavy overlap between pass and fail —
  confirms multivariate approach required
- Features operate on vastly different scales — standardization required

## Methods (planned)

- Missing data handling: comparison of imputation strategies
- Feature selection: variance thresholding, mutual information,
  Mann-Whitney, RFECV
- Classification: Logistic Regression baseline, Random Forest, XGBoost
- Evaluation: PR-AUC and F2-score (accounts for class imbalance)
- Interpretability: SHAP feature importance analysis

## Status

- [x] Data acquisition and EDA (July 27, 2026)
- [ ] Data preprocessing and feature selection
- [ ] Baseline model development
- [ ] Advanced modeling and evaluation
- [ ] SHAP interpretability analysis
- [ ] Report write-up (IEEE format)

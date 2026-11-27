# Semiconductor Yield Prediction: ML and SHAP Interpretability Analysis

**Research area:** Smart Manufacturing for Semiconductor Fabrication  
**Dataset:** SECOM (UCI ML Repository), 1,567 wafers, 590 features, 6.64% failure rate  
**Status:** Complete, with a leakage audit (notebook 09). Report in `/reports/`

---

## Research Question

Which machine learning approach most effectively predicts wafer failure in a severely imbalanced, high-dimensional semiconductor manufacturing dataset, and which process variables drive failure predictions at the individual wafer level, including failure modes where competing sensor signals mask each other?

## Key Findings

These are the audited results from notebook 09. Notebooks 03 to 08 report the original, pre-audit numbers.

- Cross-validated PR-AUC does not separate the models: Random Forest 0.1850 ± 0.0287 and XGBoost 0.1913 ± 0.0139, against a naive baseline of 0.0664. With only 104 failures, sample size at the failure class, not model architecture, is the binding constraint.

- A leakage audit refit all preprocessing inside each fold and chose thresholds from out-of-fold training predictions only. Preprocessing leakage had no detectable effect (paired PR-AUC change of -0.0033 ± 0.0044 for Random Forest and +0.0028 ± 0.0149 for XGBoost). Threshold selection mattered much more: the original 13/21 (Random Forest) and 19/21 (XGBoost) catches came from thresholds chosen on the test set. At audited thresholds both models catch 4 of 21 test failures.

- Both models still rank failures well above chance. Flagging the top 10% of test wafers catches 10 of 21 failures with Random Forest and 5 of 21 with XGBoost (about 2 expected by chance). The earlier claim that XGBoost outperforms Random Forest does not hold up under the audit.

- SHAP analysis of the Random Forest identifies a sensor masking case (Wafer 37) in which Feature 31 suppresses failure signals from Features 59 and 103. Neither audited model flags this wafer, so there is no evidence that gradient boosting recovers masked failures.

- Predictive signal is spread across 237 of 446 features for 80% of SHAP importance, which challenges Pareto-based feature selection and suggests yield failure reflects multi-causal process interactions.

## Project Structure

```
semiconductor-manufacturing-research/
├── projects/
│   └── 01_secom_yield_prediction/
│       ├── data/
│       │   ├── raw/          # Original SECOM files (download from UCI)
│       │   └── processed/    # Preprocessed splits and model artifacts
│       ├── notebooks/        # in order 01-09
│       ├── reports/
│       │   ├── figures/      # All generated figures
│       │   └── secom_yield_prediction_report.pdf
│       └── README.md         # Detailed project documentation
├── notes/
│   └── daily_log/            # Research journal entries
├── applications/
└── README.md                 # This file
```

## Notebooks

Run in order from 01 to 09. Notebooks 03 to 08 read the files saved by notebook 02. Notebook 09 starts from the raw data and does not depend on the earlier notebooks.

| Notebook                 | Purpose                                                                                       | Key Output                                                                                                         |
| ------------------------ | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| 01_data_exploration      | Load and characterize the SECOM dataset: missing data, class imbalance, feature distributions | 3 figures, 6.64% failure rate confirmed, missing data quantified                                                   |
| 02_preprocessing         | Document and execute all preprocessing decisions with written justification                   | 446 features retained, 80/20 stratified split, processed data saved                                                |
| 03_baseline_model        | Logistic regression baseline with naive baseline comparison and threshold analysis            | PR-AUC=0.1497, 4/21 failures caught at threshold 0.55 (test-set threshold, original)                               |
| 04_random_forest         | Train Random Forest, assess overfitting, analyze feature importance                           | PR-AUC=0.2743 (single split), 13/21 at threshold 0.20 (test-set threshold, original)                               |
| 05_shap_interpretability | SHAP TreeExplainer on individual wafer predictions, sensor masking analysis                   | Beeswarm and waterfall plots, Feature 31 masking case identified                                                   |
| 06_xgboost               | Train XGBoost with regularization, compare early stopping vs fixed trees                      | Original per-wafer comparison (test-set threshold, superseded by notebook 09)                                      |
| 07_cross_validation      | 5-fold stratified CV across all models, controlled missingness experiment                     | Original CV PR-AUC: LR=0.177, RF=0.196, XGB=0.208 (superseded by notebook 09); missingness experiment inconclusive |
| 08_sensitivity_analysis  | Test preprocessing robustness across 30%, 50%, 70% missingness thresholds                     | PR-AUC range=0.012; not re-run under the audited pipeline                                                          |
| 09_leakage_audit         | Re-run CV and threshold selection from raw data with every fitted step inside the folds       | CV PR-AUC: RF=0.1850, XGB=0.1913; both models catch 4/21 at audited thresholds                                     |

## Reproducing the Analysis

```bash
conda create -n research python=3.11
conda activate research
pip install numpy pandas matplotlib seaborn scikit-learn xgboost shap joblib scipy
jupyter lab
```

Open notebooks in order 01 to 09. Raw data must be downloaded from the
[UCI ML Repository](https://archive.ics.uci.edu/dataset/179/secom)
and placed in `projects/01_secom_yield_prediction/data/raw/`.

Two files required:

- `secom.data`: feature matrix (1,567 × 590)
- `secom_labels.data`: pass/fail labels and timestamps

## Report

The full IEEE-format report is available at:  
`projects/01_secom_yield_prediction/reports/secom_yield_prediction_report.pdf`

The report covers dataset characterization, preprocessing decisions, comparative model evaluation with cross-validated PR-AUC, SHAP interpretability analysis, a controlled missingness experiment, a preprocessing sensitivity analysis, and the leakage audit.

## Research Identity

I am an Industrial and Systems Engineering graduate pursuing graduate study in semiconductor manufacturing intelligence beginning Fall 2027. My research focus sits at the intersection of machine learning interpretability and manufacturing process engineering: building models that not only predict yield outcomes but explain which process variables cause failure and why, in terms actionable for process engineers. This project emerged from the recognition that semiconductor yield prediction is not primarily a modeling problem but a signal characterization problem: with 590 sensors per wafer, 6.64% failure rates, and failure signals distributed across hundreds of process variables, the research challenge is identifying which signals matter, at what thresholds, and how they interact, not simply achieving higher accuracy. I am looking to join a research group working on data-driven manufacturing intelligence, process monitoring, or ML applications in semiconductor fabrication, where engineering domain knowledge and advanced ML methods are developed together rather than in isolation.

## Background

B.S. Industrial and Systems Engineering, Youngstown State University (ABET-accredited), May 2026. Currently developing research skills independently through a structured ML research project on semiconductor yield prediction, with a second project in progress targeting packaging integration and defect classification. Targeting Fall 2027 MS admission in a semiconductor manufacturing or smart manufacturing program with research assistantship.

---

_Project completed: September 2026. Leakage audit added: November 2026. Next project: Semiconductor packaging defect classification._

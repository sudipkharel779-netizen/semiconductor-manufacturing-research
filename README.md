# Semiconductor Yield Prediction: ML and SHAP Interpretability Analysis

**Research area:** Smart Manufacturing for Semiconductor Fabrication  
**Dataset:** SECOM (UCI ML Repository) — 1,567 wafers, 590 features, 6.64% failure rate  
**Status:** Complete — report available in `/reports/`

---

## Research Question

Which machine learning approach most effectively predicts wafer failure in a severely imbalanced, high-dimensional semiconductor manufacturing dataset, and which process variables drive failure predictions at the individual wafer level — including failure modes where competing sensor signals mask each other?

## Key Findings

- XGBoost achieves the highest operational recall (19 of 21 test failures caught at threshold 0.35) despite marginally lower PR-AUC than Random Forest, demonstrating that aggregate metrics and operational performance diverge under threshold optimization for imbalanced datasets.

- Cross-validated PR-AUC differences between models are within noise bounds (RF: 0.1956±0.0266, XGBoost: 0.2083±0.0260), indicating that sample size at the failure class level — not model architecture — is the binding constraint on yield prediction performance.

- SHAP analysis identifies a sensor masking failure mode in which Feature 31 suppresses failure signals from Features 59 and 103 — gradient boosting recovers this failure class through sequential error correction where Random Forest, which averages across independent trees, cannot.

- Predictive signal is distributed across 237 of 446 features to account for 80% of SHAP importance, challenging Pareto-based feature selection approaches and suggesting yield failure reflects genuinely multi-causal process interactions.

  ## Project Structure

```
semiconductor-manufacturing-research/
├── projects/
│   └── 01_secom_yield_prediction/
│       ├── data/
│       │   ├── raw/          # Original SECOM files (download from UCI)
│       │   └── processed/    # Preprocessed splits and model artifacts
│       ├── notebooks/        # in order 01–08
│       ├── reports/
│       │   ├── figures/      # All generated figures (15+ plots)
│       │   └── secom_yield_prediction_report.pdf
│       └── README.md         # Detailed project documentation
├── notes/
│   └── daily_log/            # Research journal entries
├── applications/
└── README.md                 # This file
```

## Notebooks

Run in order from 01 to 08. Each notebook is independently runnable.

| Notebook                 | Purpose                                                                                                | Key Output                                                                            |
| ------------------------ | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------- |
| 01_data_exploration      | Load and characterize the SECOM dataset — missing data, class imbalance, feature distributions         | 3 figures, 6.64% failure rate confirmed, missing data quantified                      |
| 02_preprocessing         | Document and execute all preprocessing decisions with written justification                            | 446 features retained, 80/20 stratified split, processed data saved                   |
| 03_baseline_model        | Establish logistic regression baseline with naive baseline comparison and threshold analysis           | PR-AUC=0.1497, 4/21 failures caught, optimal threshold=0.55                           |
| 04_random_forest         | Train Random Forest, assess overfitting, analyze feature importance                                    | PR-AUC=0.2743 (single-split), 13/21 failures caught, Gini importance computed         |
| 05_shap_interpretability | Apply SHAP TreeExplainer to explain individual wafer predictions and identify sensor masking effect    | Beeswarm, waterfall plots, Feature 31 masking effect identified                       |
| 06_xgboost               | Train XGBoost with regularization, compare early stopping vs fixed trees, per-wafer failure comparison | 19/21 failures caught, XGBoost recovers 6 failures RF missed including masked failure |
| 07_cross_validation      | 5-fold stratified CV across all models, controlled missingness experiment                              | CV PR-AUC: LR=0.177, RF=0.196, XGB=0.208; missingness experiment inconclusive         |
| 08_sensitivity_analysis  | Test preprocessing robustness across 30%, 50%, 70% missingness thresholds                              | PR-AUC range=0.012 — moderately robust to threshold choice                            |

## Reproducing the Analysis

```bash
conda create -n research python=3.11
conda activate research
pip install numpy pandas matplotlib seaborn scikit-learn xgboost shap joblib
jupyter lab
```

Open notebooks in order 01–08. Raw data must be downloaded from the
[UCI ML Repository](https://archive.ics.uci.edu/dataset/179/secom)
and placed in `projects/01_secom_yield_prediction/data/raw/`.

Two files required:

- `secom.data` — feature matrix (1,567 × 590)
- `secom_labels.data` — pass/fail labels and timestamps

## Report

The full IEEE-format report is available at:  
`projects/01_secom_yield_prediction/reports/secom_yield_prediction_report.pdf`

The report covers dataset characterization, preprocessing decisions, comparative model evaluation with cross-validated PR-AUC, SHAP
interpretability analysis, a controlled missingness experiment, and preprocessing sensitivity analysis. Preprint will be posted to arXiv upon finalization.

## Research Identity

I am an Industrial and Systems Engineering graduate pursuing graduate study in semiconductor manufacturing intelligence beginning Fall 2027. My research focus sits at the intersection of machine learning interpretability and manufacturing process engineering — specifically, building models that not only predict yield outcomes but explain which process variables cause failure and why, in terms actionable for process engineers. This project emerged from the recognition that semiconductor yield prediction is not primarily a modeling problem but a signal characterization problem: with 590 sensors per wafer, 6.64% failure rates, and failure signals distributed across hundreds of process variables, the research challenge is identifying which signals matter, at what thresholds, and how they interact — not simply achieving higher accuracy. I am looking to join a research group working on data-driven manufacturing intelligence, process monitoring, or ML applications in semiconductor fabrication, where engineering domain knowledge and advanced ML methods are developed together rather than in isolation.

## Background

B.S. Industrial and Systems Engineering, Youngstown State University (ABET-accredited), May 2025. Currently developing research skills independently through a structured ML research project on semiconductor yield prediction, with a second project in progress targeting packaging integration and defect classification. Targeting Fall 2027 MS admission in a semiconductor manufacturing or smart manufacturing program with research assistantship.

---

_Project completed: September 2026. Next project: Semiconductor packaging
defect classification._

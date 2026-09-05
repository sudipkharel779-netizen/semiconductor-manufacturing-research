Date: August 27, 2026 | Day: 4

## What I completed
- [x] Created 03_baseline_model.ipynb
- [x] Loaded processed train/test data from data/processed/
- [x] Applied StandardScaler fit on training data only (leakage prevention)
- [x] Established naive baseline (always predict pass)
- [x] Trained logistic regression with balanced class weights
- [x] Evaluated using PR-AUC, ROC-AUC, confusion matrix
- [x] Threshold sweep analysis (0.10 to 0.85)
- [x] Generated 2 research figures committed to GitHub
- [x] Written baseline model summary (mini results section)

## Why logistic regression as baseline
Baseline sets the floor — everything else must beat it. Logistic 
regression is interpretable: coefficients directly show which features 
push toward failure. It predicts probabilities for binary classification 
(pass/fail), not continuous values like linear regression. This makes 
it a credible starting point, not just a weak model to beat.

## Naive baseline finding
A model that predicts pass for every wafer achieves 93.31% accuracy 
while catching zero failures. Accuracy is meaningless for imbalanced 
problems where missing a failure carries significant manufacturing cost. 
This is the floor — any model that cannot substantially beat this on 
recall has no practical value.

## Key technical insights
- class_weight='balanced' forces the model to prioritize catching 
  failures by penalizing missed failures more heavily. More wafers 
  flagged as failure → high recall, but also more false alarms → 
  lower precision.
- Feature scaling not only prevents large-range features from dominating 
  predictions, but also accelerates gradient descent convergence during 
  logistic regression training (converged in just 83 iterations).
- Threshold selection is critical for imbalanced problems — default 0.5 
  is designed for balanced data and is almost certainly wrong here.

## Results
PR-AUC (primary):   0.1497  
ROC-AUC (secondary): 0.6208  

Confusion Matrix:
- True Negatives  (correct pass):        259
- False Positives (pass → predicted fail): 34
- False Negatives (fail → predicted pass): 17 ← most costly
- True Positives  (correct fail):           4

Failures caught: 4 out of 21 (19.0%)

## Threshold analysis finding
The threshold sweep reveals that logistic regression probability 
estimates are poorly calibrated — failures caught remains flat at 4 
across thresholds 0.15 to 0.55, indicating the model assigns similar 
probability scores to both passing and failing wafers. Best recall of 
28.6% achieved only at threshold 0.10 with 59 false alarms. This 
confirms a nonlinear model with better class separation is required.

Concrete tradeoff: At threshold 0.3 we catch the same 4 failures but 
generate 8 additional false alarms compared to threshold 0.5 
(42 vs 34 false alarms).

## PR-AUC vs ROC-AUC interpretation
PR-AUC of 0.1497 is 2.3x better than random (0.067) but still very 
poor in practical terms — catching only 4 of 21 failures tells the 
real story. ROC-AUC of 0.621 looks more impressive but is optimistic 
for imbalanced data because it uses the large negative class in the 
denominator. PR-AUC is the honest metric here.

## Baseline model summary (mini results section)

**What we did:**
A logistic regression classifier with balanced class weights was trained 
on the preprocessed SECOM dataset (446 features, 1,253 training samples) 
and evaluated on a held-out test set of 314 samples containing 21 failures. 
Features were standardized using a scaler fit on training data only to 
prevent data leakage. A naive baseline (always predict pass) was first 
established, achieving 93.31% accuracy with zero failures caught.

**What we found:**
The logistic regression baseline achieved a PR-AUC of 0.150 compared to 
a random classifier baseline of 0.067 — approximately 2.3x better than 
random. At the default 0.5 threshold, the model caught 4 out of 21 failures 
(19% recall) with 34 false alarms. Threshold analysis across 0.10 to 0.85 
revealed that failures caught remained flat at 4 across a wide range, 
with maximum recall of 28.6% achieved only at threshold 0.10 — generating 
59 false alarms. The optimal F1 threshold was 0.55 with F1=0.140.

**What this means:**
The baseline demonstrates that linear decision boundaries are insufficient 
to meaningfully separate passing and failing wafers in this high-dimensional, 
severely imbalanced dataset. The flat threshold response confirms the model 
assigns similar probability scores to both classes — indicating poor class 
separation rather than a threshold calibration problem. This motivates the 
use of ensemble methods such as Random Forest and XGBoost, which can capture 
nonlinear feature interactions and provide better probability calibration 
for the minority failure class.

## Where logistic regression fails
- Assumes linear relationships — cannot capture nonlinear sensor interactions
- High feature-to-sample ratio (446 features, 1,253 samples) makes 
  coefficient estimation unstable
- Random Forest and XGBoost address both problems — nonlinear interactions 
  through tree splitting, high dimensionality through feature subsampling

## Open questions
1. Will ensemble methods like Random Forest show meaningfully better 
   class separation on SECOM, or is the signal simply too weak?
2. At what PR-AUC threshold does a model become practically useful 
   for a real semiconductor fab — what is the industry standard?

## Connection to research identity
Four days of documented, GitHub-committed research work now exists — 
EDA, preprocessing pipeline, and baseline modeling — all with written 
justifications. This is the foundation of the professor outreach 
portfolio for October.
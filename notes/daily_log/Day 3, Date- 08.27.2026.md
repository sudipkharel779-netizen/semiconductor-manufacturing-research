1. **Did the number match your EDA? If it differs slightly, why might it differ?"**
--> It matched exactly here. **variance calculation depends on which values are present.** If missingness patterns differ between two runs — say you filtered something differently — the zero variance count could change slightly.here since we loaded same data both the times at eda and here, they are same.

2. **Is the pattern of missing data 9 (mean was 1.6% )in those column  after removing column with more than 50% missing values and  zero variance in SECOM correlated with wafer failure? If sensors drop out during abnormal process conditions, the missingness pattern is a feature, not noise.

3. ## Evaluation Metric Decision: PR-AUC over ROC-AUC

**ROC-AUC limitation:** ROC-AUC uses False Positive Rate which has the 
large negative class (1,463 passes) in the denominator. Even misclassifying 
many passed wafers produces a small FPR — making the model look better 
than it is for the minority failure class.

**PR-AUC advantage:** Precision and Recall both use the minority class 
in their denominators. PR-AUC directly measures how well the model 
identifies the 104 failures without being diluted by the 1,463 passes.

**Cost asymmetry in semiconductor manufacturing:**
- False negative (predict pass, actually fails) → wafer continues through 
  hundreds of additional process steps, consuming time, labor, and materials 
  before failing at final test. Highest cost.
- False positive (predict fail, actually passes) → engineer re-inspects 
  a good wafer. Lower cost — wastes time but not an entire process sequence.

**Conclusion:** False negatives are far more expensive. We optimize for 
high Recall — catching as many real failures as possible — using PR-AUC 
and F2-score as primary evaluation metrics.
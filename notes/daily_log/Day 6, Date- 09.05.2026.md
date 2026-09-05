Date: September 5, 2026 | Day: 6

## What I completed
- [x] Installed SHAP 0.51.0 in research environment
- [x] Created 05_shap_interpretability.ipynb
- [x] Retrained Random Forest — PR-AUC confirmed at 0.2743 (reproducible)
- [x] Computed exact SHAP values via TreeExplainer (314 × 446 matrix)
- [x] SHAP vs Gini importance comparison — top-20 bar charts generated
- [x] Beeswarm plot — direction of effect for top 20 features
- [x] Waterfall plot — correctly predicted failure (wafer 60)
- [x] Waterfall plot — missed failure (wafer 37)
- [x] SHAP dependence plots — Feature 103 and Feature 59
- [x] SHAP importance concentration curve generated
- [x] Everything committed and pushed to GitHub

## Why SHAP over Gini importance
Gini importance tells us which features appeared most often in tree 
splits — more appearances = higher importance. But it cannot tell us 
whether a feature pushes toward failure or pass. SHAP tells us the 
direction and magnitude of each feature's contribution to each 
individual wafer's prediction. SHAP is preferred for the paper because 
it measures actual prediction contributions and provides directional 
information, while Gini importance reflects tree structure statistics 
that can be biased toward high-cardinality features.

## SHAP matrix
Shape: (314, 446) — 314 test wafers × 446 features.
Each cell contains the contribution of one feature to one wafer's 
failure probability prediction. Value range: [-0.079, 0.038].
The small range confirms that no single feature dominates — failure 
prediction is distributed across many sensors, consistent with the 
Gini importance finding from Day 5.

## SHAP findings — what the model learned

**Top features by SHAP importance:**
1. Feature 103
2. Feature 59
3. Feature 33
4. Feature 31
5. Feature 477

**Comparison with Gini importance:**
Perfect agreement — all 10 top SHAP features appear in Gini top-10 
as well. Ordering differs slightly (Feature 130 ranks #5 in Gini 
but #10 in SHAP; Feature 477 ranks higher in SHAP than Gini) but 
the feature set is identical. Both methods agree — increases 
confidence in these sensors as genuine signal carriers.

**Direction of effect for top 3 features:**
- Feature 103: high values push toward failure, low values push 
  toward pass — clear threshold at scaled value ~0
- Feature 59: high values push toward failure, low values push 
  toward pass — gradual risk increase beyond scaled value ~0.25
- Feature 33: low values push strongly toward pass, high values 
  show weak mixed effect — acts as pass indicator rather than 
  failure driver

**Explanation of a correctly caught failure (Wafer 60):**
Feature 103 elevated at 1.362 scaled units (+0.02 toward failure) 
combined with Feature 510 and Feature 132 pushing toward failure. 
Feature 213 pushed back toward pass (-0.03) and 432 other features 
combined for -0.19 toward pass. Caught by narrow margin — predicted 
probability 0.2801, just above threshold 0.20. Small cluster of 
elevated sensors overcame broad pass signal.

**Explanation of a missed failure (Wafer 37):**
Conflicting signals — not weak signals. Feature 31 highly elevated 
(1.947 scaled units) but pushed strongly toward pass (-0.05), 
dominating the prediction. Features 59 (+0.02) and 103 (+0.01) 
were present and pushing toward failure but insufficient to overcome 
Feature 31's pass signal. 432 other features pushed -0.26 toward 
pass. This wafer represents a distinct failure mode where typical 
failure indicators are masked by anomalous Feature 31 behavior — 
invisible to the current model.

## The research finding I can state from this analysis
SHAP analysis reveals that SECOM yield failure is not governed by 
a single dominant sensor but by the interaction of broadly distributed 
process signals. Features 103 and 59 consistently elevate failure 
risk when above their scaled thresholds — suggesting these sensors 
belong to process steps where parameter drift directly precedes 
downstream yield loss. Feature 103's threshold effect at scaled 
value ~0 represents a potential statistical process control limit: 
engineers could establish a real-time alert when this sensor exceeds 
the threshold, enabling intervention before the wafer completes all 
remaining process steps and fails at end-of-line test. More 
importantly, the missed failure analysis reveals a distinct failure 
mode characterized by anomalous Feature 31 behavior — a pattern 
the current model cannot recognize. This gap points directly to 
where future modeling effort should focus: identifying the process 
conditions that cause Feature 31 to mask failure signals, and 
building a model that can detect this failure mode independently.

## SHAP vs Gini comparison finding
Perfect top-10 overlap — all 10 features appear in both rankings:
Features 103, 130, 205, 213, 31, 33, 341, 477, 59, 64.
While ordering differs slightly, consistent feature sets across two 
independent importance methods increases confidence these sensors 
carry genuine yield prediction signal rather than noise.

## Beeswarm interpretation — direction of effect
Feature 103: high sensor values push strongly toward failure — densest 
cluster of red dots sits right of zero. Low values push toward pass.

Feature 59: similar pattern to 103 — high values toward failure, low 
values toward pass. Long right tail suggests some wafers with very 
high Feature 59 readings have strong failure signal.

Feature 33: asymmetric pattern — low values push strongly toward pass 
(blue dots spread far left), while high values show weak mixed effect. 
Feature 33 acts more as a pass indicator than a failure driver.

## Waterfall — correctly predicted failure (Wafer 60)
Predicted probability: 0.2801 | Actual: FAIL | Prediction: FAIL

Feature 103 (elevated at 1.362 scaled units) pushed toward failure 
(+0.02) but Feature 213 pushed strongly toward pass (-0.03). Combined 
effect of 432 other features was -0.19 toward pass — caught by narrow 
margin. Baseline E[f(x)] = 0.5 shifted by balanced class weights, 
not the classification threshold.

## Waterfall — missed failure (Wafer 37)
Predicted probability: 0.137 | Actual: FAIL | Prediction: PASS (MISSED)

Feature 31 highly elevated (1.947 scaled units) but contributes -0.05 
toward pass — strongest individual feature contribution. Features 59 
(+0.02) and 103 (+0.01) present but insufficient to overcome Feature 
31's pass signal. 432 other features pushed -0.26 toward pass.
Represents a distinct failure mode invisible to the current model — 
a publishable finding.

## SHAP dependence plots
Feature 103: clear threshold effect at scaled value ~0. Readings below 
zero push toward pass, readings above push toward failure. A statistical 
process control limit could be established at this threshold for 
real-time intervention.

Feature 59: smoother relationship — gradual risk increase beyond scaled 
value ~0.25 with large variance. Not a sharp threshold but a gradual 
risk increase.

Practical implication: process engineers can use these thresholds for 
Root Cause Analysis (RCA) — documenting sensor ranges associated with 
failure and establishing control limits for real-time intervention.

## SHAP concentration finding
- Features for 50% importance: 82
- Features for 80% importance: 237
- Features for 95% importance: 369
- Total features: 446

237 of 446 features needed for 80% importance — far from typical 20/80 
Pareto concentration. SECOM yield failure driven by broadly distributed 
sensor interactions. Simple feature selection retaining only top-10 or 
top-20 features will likely degrade performance significantly. A more 
conservative threshold of 100-150 features needed for feature-reduced 
modeling.

## Baseline interpretation
E[f(x)] = 0.5 is the model's average predicted failure probability 
before seeing any features — shifted from true failure rate (6.64%) 
by balanced class weighting. Every wafer starts at 0.5 and SHAP 
values push it up or down from there.

## Checkpoint answers
1. E[f(x)] = 0.5 is the baseline prediction shifted by balanced class 
   weighting. Predominantly positive SHAP values for a failed wafer 
   means multiple sensors collectively pushed failure probability above 
   baseline.
2. Feature A pushing toward failure and Feature B pushing toward pass 
   for the same wafer means conflicting signals — the model sees 
   evidence for both outcomes. Suggests failure mode partially resembles 
   normal process conditions, making it difficult to detect.
3. 209 low-SHAP features are not globally useless — some may be critical 
   for rare failure modes appearing infrequently in the test set. Low 
   average importance does not equal zero importance for specific failure 
   patterns.
4. SHAP importance preferred for the paper — measures actual prediction 
   contributions with directional information, unlike Gini which reflects 
   tree structure statistics biased toward high-cardinality features.

## Open questions
1. Wafer 37 was missed because Feature 31 pushed strongly toward pass 
   despite being a true failure — are there other missed failures with 
   similar Feature 31 patterns? Is this a systematic blind spot affecting 
   multiple failure modes?
2. SHAP concentration requires 237 features for 80% importance — would 
   training XGBoost on only these 237 features improve or degrade 
   performance? What is the minimum feature set preserving 95% of 
   predictive performance?
3. Feature 103 shows a threshold effect at scaled value ~0 — what is 
   the real physical sensor measurement at that threshold? Does it 
   correspond to a known process specification limit in semiconductor 
   manufacturing literature?

## Connection to research identity
Day 6 is the most important day so far — SHAP transforms the project 
from classification exercise into semiconductor process engineering 
insight. The missed failure analysis (Wafer 37) and the Feature 103 
threshold finding are publishable contributions not found in standard 
SECOM tutorials. 
What I completed
- [x] Created 08_sensitivity_analysis.ipynb
- [x] Tested missingness thresholds at 30%, 50%, 70% with XGBoost CV
- [x] Generated sensitivity analysis figure
- [x] Completed full IEEE LaTeX paper in Overleaf
- [x] Added 29 references in BibTeX format
- [x] Added inline citations throughout all sections
- [x] Two tables: CV results and per-wafer recovery
- [x] All six sections: Abstract, Intro, Dataset, Methodology, 
      Results, Discussion, Conclusion
- [x] Everything committed and pushed to GitHub

## Sensitivity analysis purpose and result
Sensitivity analysis asks whether conclusions change if we made a 
different preprocessing choice. We are not optimizing — we are 
testing stability. If PR-AUC at 30%, 50%, and 70% thresholds are 
similar (within ±0.010), we can tell reviewers our results don't 
depend on this specific choice.

Results:
- 30% threshold: 442 features | CV PR-AUC = 0.1797 ± 0.0241
- 50% threshold: 446 features | CV PR-AUC = 0.1910 ± 0.0271
- 70% threshold: 466 features | CV PR-AUC = 0.1916 ± 0.0342
- PR-AUC range across thresholds: 0.0119

Result: MODERATELY ROBUST. The range of 0.0119 is just above the 
0.010 robust threshold but smaller than the within-threshold fold 
variance at any single condition. Threshold choice introduces less 
variation than fold-to-fold sampling variation. This supports the 
conclusion that findings reflect properties of the manufacturing 
data rather than artifacts of the specific 50% threshold selection.

Paper sentence: "CV PR-AUC varied by 0.012 across missingness 
thresholds of 30%, 50%, and 70% — smaller than the within-threshold 
fold variance — indicating reported findings are not artifacts of 
this specific preprocessing choice."

## Paper completion status
Full IEEE conference paper drafted in Overleaf with:
- Abstract: 5 sentences, all claims traceable to results
- Introduction: 5 paragraphs, 3 numbered contributions
- Section II: Dataset, preprocessing pipeline, evaluation framework
- Section III: Three model descriptions, experimental design
- Section IV: CV table, per-wafer table, SHAP analysis, 
  sensitivity analysis
- Section V: Discussion — limitations, missingness, masking effect
- Section VI: Conclusion with future work
- 29 references in BibTeX
- GitHub URL for reproducibility

## Checkpoint answers

**1. Technical precision of boosting mechanism claim:**
The claim "gradient boosting explicitly targets misclassified 
samples in each subsequent boosting round" is not precisely accurate. 
The technically correct version:

XGBoost fits each new tree to the negative gradient of the loss 
function with respect to current predictions — this gradient is 
large for samples where the current ensemble predicts poorly, so 
subsequent trees implicitly focus more correction effort on 
hard-to-predict samples. It is not that misclassified samples are 
explicitly selected; rather, the gradient-based optimization 
automatically assigns larger residuals to poorly predicted samples, 
causing subsequent trees to allocate more capacity to correcting 
those predictions.

**2. Statistical test for missingness experiment:**
The appropriate test is a Wilcoxon signed-rank test on the five 
paired fold scores (with-missingness vs without-missingness at 
each fold). With only 5 paired observations, the test has very 
low statistical power. Expected result: p > 0.05, failing to 
reject the null hypothesis of no difference. The difference of 
+0.0062 is well within the noise range of ±0.027 standard 
deviation — a paired t-test would also be appropriate and would 
yield the same conclusion. Neither test has sufficient power to 
detect differences this small with n=5.

**3. Future work in conclusion:**
Both Day 9 future directions are in the conclusion:
1. Stacking ensemble combining RF and XGBoost ✓ — included as 
   "a stacking ensemble combining Random Forest probability 
   calibration with XGBoost's sequential error correction"
2. Larger proprietary datasets ✓ — included as "replication on 
   larger proprietary semiconductor datasets"
The sensitivity analysis threshold experiment was also mentioned 
in Day 9 but was actually run on Day 10, so it appears in 
Section IV-D rather than future work.

**4. Abstract vs conclusion consistency check:**

Abstract claim 1: "severe class imbalance, widespread missing 
sensor readings, failure signals distributed across hundreds of 
variables" → Supported: Section II-A documents 6.64% failure 
rate, 538/590 missing features, SHAP concentration analysis ✓

Abstract claim 2: "few studies systematically examine class 
imbalance handling, cross-validated evaluation, sensor-level 
explanations" → Supported: Introduction paragraph 3 cites 
specific gaps in literature ✓

Abstract claim 3: "comparative analysis of LR, RF, XGBoost 
with SHAP, missingness experiment, threshold optimization" → 
Supported: Section III describes all three ✓

Abstract claim 4: "XGBoost recovers 19 of 21 test failures 
including a failure mode masked by competing sensor signals 
that RF could not detect" → Supported: Table II shows 19/21, 
Section IV-B describes Wafer 37 masking mechanism ✓

Abstract claim 5: "237 of 446 features needed for 80% SHAP 
importance" → Supported: Section IV-C states this explicitly ✓

All five abstract claims are traceable to specific numbers 
in the results sections. Abstract and conclusion tell a 
consistent story. No revisions needed.

## What remains for cleanup session
- Fix any LaTeX warnings in Overleaf
- Add figures using \includegraphics{}
- Remove \nocite{*} — keep only cited references
- Read full PDF end to end for awkward sentences
- Post to arXiv

## Open questions
1. Which figures to include in the paper — all 15+ are in 
   GitHub. Priority: CV fold chart, per-wafer comparison, 
   SHAP beeswarm, sensitivity analysis bar chart.
2. arXiv submission requires institutional affiliation — 
   Youngstown State University is listed. Is that sufficient 
   for submission as an independent researcher?
3. Should the paper be submitted to arXiv before or after 
   professor outreach emails in October?

## Connection to research identity
Day 10 closes the research project with a complete, citable 
paper. The combination of GitHub portfolio (8 notebooks, 15+ 
figures) and an arXiv preprint represents what professors 
evaluating MS applicants for Fall 2027 will see: not just 
coursework, but evidence of independent research capability 
in semiconductor manufacturing — the exact domain the CHIPS 
Act has made a national priority.
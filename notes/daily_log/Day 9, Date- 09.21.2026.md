## What I completed
- [x] Set up IEEE Conference template in Overleaf
- [x] Wrote Title and Author block
- [x] Wrote Abstract — 5 sentences, all supported by actual results
- [x] Wrote Introduction — 5 paragraphs with numbered contributions
- [x] Wrote Section II — Dataset, Preprocessing Pipeline, 
      Evaluation Framework
- [x] Wrote Section III — Methodology, all three models documented,
      experimental design including SHAP and missingness experiment
- [x] GitHub URL included for reproducibility statement

## What changed in my understanding today
Writing the abstract sentence by sentence forced me to choose what 
to claim. I included the noise bounds finding — that model differences 
are within CV uncertainty — even though it weakens the "XGBoost wins" 
narrative. I made this choice because the research result was different 
from what we predicted initially. We started looking for the best model 
to achieve high PR-AUC, but the data itself revealed something more 
important: SECOM's severe imbalance means the noise present in the 
data overwhelms model differences. Reporting that honestly is more 
credible than overstating XGBoost's advantage. A reviewer who catches 
an oversold claim rejects the paper. A reviewer who sees honest 
uncertainty reporting trusts the rest of the findings.

## The contribution I am most confident in
The SHAP-based sensor masking finding is the most novel contribution. 
The cross-validated comparison is methodologically important but not 
unique — others have run CV on SECOM before. The missingness experiment 
produced an inconclusive result, which is still a contribution but not 
a positive finding. The Wafer 37 masking effect — where Feature 31 
suppresses failure signals from Features 59 and 103, and XGBoost's 
sequential correction recovers it where Random Forest cannot — is a 
specific, empirically demonstrated finding that connects ML behavior 
to semiconductor process engineering. That connection is what makes 
this work more than a benchmark comparison.

## What I would do differently with 6 more months

Modeling decision: Train a stacking classifier combining Random Forest 
and XGBoost predictions — RF offers better probability calibration and 
lower false alarms while XGBoost catches failure modes RF misses. A 
stacking ensemble could achieve the best of both: high recall with 
reduced false alarm cost. This directly addresses the undetected 
Wafers 56 and 241.

Preprocessing decision: Run sensitivity analysis on the missingness 
threshold — repeat the full pipeline at 30%, 40%, 50%, 60%, and 70% 
thresholds and report whether CV PR-AUC changes meaningfully. The 
current 50% threshold was a defensible choice but not empirically 
validated. If results are stable across thresholds, the finding is 
robust. If they change, the threshold choice becomes a key variable 
to optimize.

## Connection to NIW
This paper contributes to U.S. semiconductor manufacturing capability 
by demonstrating that traditional ML evaluation metrics are insufficient 
for yield prediction under severe class imbalance — and that 
interpretable models identifying specific sensor thresholds and failure 
modes provide actionable intelligence that accuracy-based benchmarks 
cannot, directly supporting the CHIPS Act priority of advancing 
domestic semiconductor manufacturing intelligence.

## Checkpoint answers

1. Including noise bounds in the abstract was the right choice — 
   honest uncertainty reporting is more credible to reviewers than 
   overselling a marginal model difference. A paper that claims 
   "XGBoost significantly outperforms Random Forest" when CV standard 
   deviations overlap is scientifically dishonest and will be caught 
   in review.

2. The SHAP masking finding is most novel — a specific, empirically 
   demonstrated sensor interaction effect connecting ML behavior to 
   process engineering is more valuable than a methodological 
   comparison or a null experiment result.

3. Missingness experiment showed inconclusive result — but it is 
   still a contribution. An inconclusive experiment that was properly 
   designed and honestly reported prevents other researchers from 
   pursuing the same hypothesis without evidence. The contribution 
   is not the positive finding — it is the test itself. Reporting a 
   null result saves the research community time and resources. 
   That is a legitimate scientific contribution.

4. Reviewer response on preprocessing sensitivity: run full pipeline 
   at multiple thresholds (30%, 50%, 70%) and report stability of 
   CV PR-AUC across choices. If stable, findings are robust. If not, 
   threshold becomes a key variable to optimize.

## Open questions
1. Would a stacking ensemble (RF + XGBoost) recover Wafers 56 and 
   241 — the two failures no model detected?
2. Does the Feature 103 threshold effect at scaled value ~0 correspond 
   to a known process specification limit in semiconductor 
   manufacturing literature?
3. Would the missingness hypothesis show a significant result on a 
   larger semiconductor dataset with more than 104 failures?

## Connection to research identity
Day 9 produced the first complete draft of an academic paper from 
this project. Writing the abstract and introduction forced clarity 
about what the actual contribution is — not "XGBoost wins" but 
"yield failure prediction is fundamentally limited by sample size, 
model choice matters at the operational level, and SHAP identifies 
specific sensor masking effects that neither accuracy metrics nor 
aggregate model comparisons reveal." 


  











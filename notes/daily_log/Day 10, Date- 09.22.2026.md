**Sensitivity analysis** asks: _do our conclusions change if we made a different preprocessing choice?_ We're not trying to find a better threshold — we're trying to show that our findings are **stable** regardless of which reasonable threshold we picked.

If PR-AUC at 30%, 50%, and 70% are all similar (within ±0.010), we can tell the reviewer: "Our results don't depend on this specific choice — any reasonable threshold gives the same conclusion."

If they're very different, we have a problem — our findings might be an artifact of the 50% choice.

The range of 0.0119 across three missingness thresholds (30%, 50%, 70%) falls in the moderately robust category. The difference between 30% and 70% thresholds is smaller than the standard deviation within any single threshold condition — meaning threshold choice introduces less variation than fold-to-fold sampling variation. This supports the conclusion that our reported findings are not artifacts of the specific 50% threshold selection, though some sensitivity exists.

CV PR-AUC varied by 0.012 across missingness thresholds of 30%, 50%, and 70% --- smaller than the within-threshold fold variance --- indicating that reported findings are not artifacts of this specific preprocessing choice
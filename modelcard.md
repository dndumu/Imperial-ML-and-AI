# Model Card: AHEBO

## 1. Overview
Adaptive Hybrid Ensemble Bayesian Optimisation (AHEBO)

## 2. Intended Use
Suitable for:
- Small data optimisation
- Expensive evaluation tasks

Not suitable for:
- Large datasets
- Deterministic problems

## 3. Details
Adopts an overarching strategy of exploration, then balanced exploration and exploitation, and finally exploitation focused.
Uses PCA(1D) EVR to split functions into low, mid and high dimensionality groups with a different approach for each group.

Group A: For f1–f3 (low dimension)
1.	“candidate pool ensemble”: full-space + PCA(1D/2D)-guided candidates, pick best by acquisition
2.	multi-kernel GP (Matern + RBF)
Planned schedule
•	Steps 1–4: UCB, kappa=5 (exploration)
•	Steps 5–9: EI, xi=0.02–0.05 (balanced)
•	Steps 10–13: EI, xi=0.0–0.01 (exploit)

Group B: For f4–f6 (mid dimension)
1.	GP + Random Forest ensemble
2.	Acquisition ensemble: UCB early, EI late
Planned schedule
•	Steps 1–5: UCB, kappa=4.5
•	Steps 6–9: UCB kappa=2.5 or EI xi=0.03
•	Steps 10–13: EI, xi=0.01

Group C:  For f7–f8 (high dimension)
1.	Trust-region BO (TuRBO-style) + occasional global step
2.	GP + ExtraTrees ensemble
Planned schedule
1.	Steps 1–3: global exploration (UCB kappa=5–7 or max-std)
2.	Steps 4–13: trust-region / local BO around best.	use EI (xi=0.01–0.03) or moderate UCB (kappa=2)

## 4. Performance
f1: max=0.0001255, mean=-0.0001864, std=0.0008544
f2: max=0.6631, mean=0.2166, std=0.2937
f3: max=-0.03484, mean=-0.1241, std=0.1048
f4: max=-0.8539, mean=-15.09, std=9.407
f5: max=4695, mean=1033, std=1643
f6: max=-0.2971, mean=-1.265, std=0.5725
f7: max=1.365, mean=0.2978, std=0.4015
f8: max=9.81, mean=8.014, std=1.023

Example insight:
- f8 shows high variability (std large), indicating unstable landscape
- f1 shows low variance and smoother function

## 5. Assumptions and Limitations
Assumes local smoothness and surrogate reliability.

Failure case:
- SVR performed poorly on f5 due to scale/heteroscedasticity

## 6. Ethical Considerations
Transparency ensures reproducibility and interpretability.

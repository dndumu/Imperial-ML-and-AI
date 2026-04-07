# Model Card: AHEBO

## 1. Overview
Adaptive Hybrid Ensemble Bayesian Optimisation (AHEBO) approach for finding the maximum of eight unknown (black-box) functions with 2D, 2D, 3D, 4D, 4D, 5D, 6D and 8D inputs respectively, with each function having one scalar output, with very limited known data for each function and limited query budget. 

## 2. Intended Use
Suitable for:
- Small data optimisation
- Expensive evaluation tasks

Not suitable for:
- Large datasets
- Deterministic problems

## 3. Details
AHEBO adopts an overarching strategy of exploration, then balanced exploration and exploitation, and finally exploitation focused.  It uses PCA(1D) EVR to split functions into low, mid and high dimensionality groups with a different approach for each group.

Group A: For f1–f3 (low dimension)
1.	“candidate pool ensemble”: full-space + PCA(1D/2D)-guided candidates, pick best by acquisition
2.	multi-kernel GP (Matern + RBF)

Planned schedule
1.	Steps 1–4: UCB, kappa=5 (exploration)
2.	Steps 5–9: EI, xi=0.02–0.05 (balanced)
3.	Steps 10–13: EI, xi=0.0–0.01 (exploit)

Group B: For f4–f6 (mid dimension)
1.	GP + Random Forest ensemble
2.	Acquisition ensemble: UCB early, EI late

Planned schedule
1.	Steps 1–5: UCB, kappa=4.5
2.	Steps 6–9: UCB kappa=2.5 or EI xi=0.03
3.	Steps 10–13: EI, xi=0.01

Group C:  For f7–f8 (high dimension)
1.	Trust-region BO (TuRBO-style) + occasional global step
2.	GP + ExtraTrees ensemble

Planned schedule
1.	Steps 1–3: global exploration (UCB kappa=5–7 or max-std)
2.	Steps 4–13: trust-region / local BO around best.	use EI (xi=0.01–0.03) or moderate UCB (kappa=2)

## 4. Performance
| Function | Max | Mean | STD | 
|----------|-----|--------------|-------------|
| f1 |0.0001255|-0.0001864|0.0008544|
| f2 |0.6631|0.2166|0.2937|
| f3 |-0.03484|-0.1241|0.1048|
| f4 |-0.8539|-15.09|9.407|
| f5 |4695|1033|1643|
| f6 |-0.2971|-1.265|0.5725|
| f7 |1.365|0.2978|0.4015|
| f8 |9.81|8.014|1.023|

Example insight:
- f8 shows high variability (std large), indicating unstable landscape
- f1 shows low variance and smoother function

## 5. Assumptions and Limitations
Assumes local smoothness and surrogate reliability.

Failure case:
- SVR performed poorly on f5 due to scale/heteroscedasticity

## 6. Ethical Considerations
Transparency ensures reproducibility and interpretability.

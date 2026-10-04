# Ensemble BO for functions 4–6

## Purpose

This strategy targets `function_4`, `function_5`, and `function_6`, which occupy the intermediate-dimensional group: four, four, and five inputs respectively. Their observed structure is less directly captured by a single low-dimensional projection, so the approach combines a smooth probabilistic model with a tree ensemble and feature-screening signals.

## Model ensemble

- An ARD Gaussian process supplies the primary smooth surrogate and uncertainty estimate.
- A Random Forest supplies a non-parametric prediction and an ensemble-based uncertainty estimate.
- The GP and Random Forest predictions are blended, with a disagreement term contributing to predictive uncertainty.
- ARD length scales, Random Forest feature importance, and LASSO coefficients are combined as soft dimension weights rather than used as hard feature deletion.

This preserves potentially useful weak dimensions while allowing candidate generation to focus on dimensions that the evidence supports.

## Candidate generation and local refinement

The candidate pool includes global and local samples around the incumbent best point, with distances scaled by the combined feature weights. A diversity bonus favours candidates that are not too close to previous observations.

When enough local observations are available, an optional small neural network is used only as a local helper. It can contribute:

- a local ranking signal;
- directional proposals from the fitted gradient;
- a modest adjustment to the dimension weights.

The neural network does not replace the GP/Random Forest ensemble or the acquisition rule.

## Acquisition schedule

| Phase | Steps | Acquisition | Purpose |
|---|---:|---|---|
| Early | 1–5 | UCB with a high exploration weight | Explore uncertain regions |
| Middle | 6–9 | Lower-weight UCB or EI | Balance uncertainty and improvement |
| Late | 10–13 | EI with a low improvement threshold | Exploit the most promising regions |

The final score combines the GP/Random Forest acquisition value, the distance-diversity bonus, and—when enabled—the neural-network ranking bonus.

## Why this fits functions 4–6

These functions have enough dimensions for interactions and non-smooth behaviour to matter, but not so many that a fully local trust-region strategy is necessary from the outset. The GP captures smooth trends, the Random Forest provides robustness to irregular structure, and soft feature weighting avoids overcommitting to one diagnostic.

## Limitations

- Tree-based uncertainty is an empirical dispersion measure rather than a calibrated posterior.
- LASSO and importance estimates can be unstable with sparse observations.
- The neural-network helper is only appropriate after a minimum local sample size is available.
- Large output scales, especially for `function_5`, can make surrogate fitting and comparison more sensitive.


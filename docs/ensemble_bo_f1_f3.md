# Ensemble BO for functions 1–3

## Purpose

This strategy targets `function_1`, `function_2`, and `function_3`, which have only two or three input dimensions and show comparatively stronger low-dimensional structure. The approach uses that structure to generate useful candidates while retaining a probabilistic Bayesian-optimisation decision rule.

## Model ensemble

- Two Gaussian-process surrogates are fitted with automatic relevance determination (ARD): a Matern kernel and an RBF kernel.
- Predictions are combined by averaging the surrogate means and combining within-model and between-model uncertainty.
- Inverse learned length scales provide dimension weights for distance calculations and local candidate generation.
- A neural-network surrogate is deliberately excluded because the available sample sizes are too small for a reliable additional learner in this group.

## Candidate generation

The candidate pool combines:

1. Local samples around the incumbent best point.
2. Candidates guided by one-dimensional PCA.
3. Candidates guided by two-dimensional PCA when the function has enough dimensions.

Candidates are filtered by an SVC gate trained in PCA space. The gate labels the upper response quantile as promising and retains the most plausible high-value candidates. A dimension-scaled distance bonus also preserves diversity and reduces repeated sampling of already-observed points.

## Acquisition schedule

The schedule follows the overall 13-query budget:

| Phase | Steps | Acquisition | Purpose |
|---|---:|---|---|
| Early | 1–4 | UCB, high exploration weight | Identify promising regions while uncertainty is high |
| Middle | 5–9 | EI with moderate improvement threshold | Balance predicted improvement and uncertainty |
| Late | 10–13 | EI with a low improvement threshold | Concentrate on the strongest candidates |

The acquisition function scores the filtered ensemble candidate pool; the highest-scoring candidate is selected for the next evaluation.

## Why this fits functions 1–3

The lower dimensionality makes PCA-guided proposals practical, while the small sample sizes make a conservative GP ensemble preferable to a larger collection of flexible models. SVC gating uses the observed high-performing region as a coarse classifier without replacing the calibrated GP acquisition score.

## Limitations

- PCA directions can be unstable when the sample is very small.
- The SVC gate depends on having both high- and lower-performing labels.
- The strategy assumes that the learned GP length scales provide useful relative dimension information.


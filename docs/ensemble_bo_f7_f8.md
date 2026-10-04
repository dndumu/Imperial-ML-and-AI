# Ensemble BO for functions 7–8

## Purpose

This strategy targets `function_7` and `function_8`, the six- and eight-dimensional functions. Sparse coverage becomes especially problematic in these dimensions, so the strategy alternates global exploration with local trust-region search and uses learned dimension weights to shape the local region.

## Model ensemble

- An ARD Matern Gaussian process models the local response surface and supplies the acquisition uncertainty.
- Local SVR permutation importance can provide an additional estimate of which dimensions matter.
- ARD and SVR importance are combined into trust-region weights.
- A local neural network can generate directional proposals using clipped, normalised, coordinate-weighted gradients.

Weak-dimension masking and noisy directional proposals reduce the risk of following a single unreliable gradient estimate too aggressively.

## Global/local schedule

The trust-region schedule begins with global coverage and then concentrates around the incumbent best point:

| Phase | Behaviour | Purpose |
|---|---|---|
| Global | Uniform candidate generation, high-UCB exploration | Find promising regions in the high-dimensional domain |
| Local | Weighted samples inside the trust region, plus a smaller global pool | Refine the best region while retaining escape options |
| Periodic global | Global exploration at a fixed frequency | Guard against premature convergence |

The initial trust-region radius is approximately 0.25 in the unit-scaled input space. Local candidate distances are scaled by the combined ARD/SVR weights.

## Neural-network gradient proposals

The neural-network helper is used only inside the local phase and only when sufficient local data is available. Its gradients are transformed into bounded candidate directions, blended with the surrogate-derived weights, and evaluated alongside ordinary local and global candidates. The GP acquisition score remains the final decision rule.

## Acquisition

Global steps use high-weight UCB to prioritise uncertainty. Local steps use UCB or EI according to the schedule, with an additional diversity bonus. The candidate with the highest final score is selected.

## Why this fits functions 7–8

High dimensionality makes uniform global search inefficient after the first promising region has been found, but sparse data makes an exclusively local strategy unsafe. The TuRBO-style alternation provides a practical compromise: exploit a weighted local neighbourhood while periodically testing the wider domain.

## Limitations

- Trust-region weights can be unreliable when the local sample is small.
- SVR permutation importance is sensitive to local subset selection and scaling.
- Neural-network gradients can amplify noise; clipping and masking reduce but do not remove that risk.
- The method remains vulnerable to missed optima outside the explored regions.


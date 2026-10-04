# Analysis leading to the three ensemble BO approaches

## Problem setting

The capstone requires maximising eight unknown scalar-output functions. The inputs are bounded in the unit interval, the functions have dimensions from two to eight, and only a small number of new queries is available for each function. The observations are therefore sparse, sequential, and decision-driven rather than an unbiased sample suitable for ordinary supervised learning.

The combined dataset contains eight function identifiers, up to eight input columns, and one response column `y`. Rows with unused input columns are interpreted according to the known dimensionality of each function.

## Initial data checks

The analysis workflow loads and validates the combined CSV, removes malformed or incomplete rows using the known input dimension for each function, tabulates the observations, and produces one response plot per function. This established the basic characteristics that an optimisation strategy had to respect:

- different input dimensionalities;
- different response scales and variances;
- sparse coverage, especially for the higher-dimensional functions;
- sequential clustering around regions that already appeared promising.

The data should therefore be treated as evidence for sequential decision-making, not as a representative random sample of each full function domain.

## Structural findings

The functions differ materially in apparent structure. Lower-dimensional functions have more usable coverage per dimension and higher explained variance in low-dimensional projections. The intermediate group has enough dimensions for feature interactions and irregularity to matter. The six- and eight-dimensional functions have lower one-dimensional PCA explained variance and much sparser coverage, making global candidate generation increasingly expensive.

The response scales also differ substantially. Some functions are close to zero or negative over much of the observed data, while `function_5` has a much larger positive scale. This argues against using one unmodified surrogate and one fixed acquisition schedule for every function.

## From findings to strategy groups

The analysis led to three groups rather than eight entirely separate algorithms:

| Group | Functions | Main evidence | Resulting design |
|---|---|---|---|
| A | `function_1`–`function_3` | Low dimension and stronger PCA structure | PCA-guided candidates, ARD GP ensemble, and SVC gating |
| B | `function_4`–`function_6` | Intermediate dimension and mixed structure | ARD GP plus Random Forest, with LASSO and importance-based soft weighting |
| C | `function_7`–`function_8` | High dimension, weak low-dimensional projection, sparse coverage | TuRBO-style global/local search, ARD GP, SVR importance, and local NN gradients |

This grouping balances adaptation with reproducibility: each group has a coherent strategy, while the same overall principles—uncertainty-aware acquisition, diversity, and adaptive dimension weighting—are retained.

## Common design principles

### Exploration and exploitation

All three approaches begin with more exploratory behaviour and become more exploitative as the query budget is consumed. UCB is useful when uncertainty should be rewarded explicitly; EI becomes more useful when the incumbent best value is informative and the search should focus on likely improvement.

### Dimension-aware candidate generation

ARD length scales provide a model-based indication of relative dimension relevance. These weights are used in scaled distances, local sampling, diversity bonuses, and—in the high-dimensional group—trust-region geometry. The weighting is deliberately soft so that a weak early signal does not permanently remove a dimension.

### Candidate pools rather than single proposal mechanisms

Each strategy builds a pool from more than one source: global samples, local samples, projection-guided samples, or model-informed directional proposals. The acquisition function then compares candidates on a common score. This reduces dependence on any single proposal heuristic.

### Conservative use of flexible models

Neural networks are used only as helpers where their contribution can be constrained: local ranking, directional proposals, or gradient-based refinement. They are not treated as the primary uncertainty model because the data volume is too small for reliable standalone neural-network Bayesian optimisation.

## Final rationale

The three ensemble BO approaches are a response to the central trade-off in the capstone: every query is valuable, but the available evidence is too limited to justify strong assumptions. The final design therefore combines simple structural diagnostics with flexible but bounded candidate generation, retains uncertainty in the acquisition score, and changes the balance between global and local search as dimensionality and coverage demand.

The approaches should be interpreted as practical, evidence-guided optimisation policies—not as claims that the underlying functions have been fully learned.


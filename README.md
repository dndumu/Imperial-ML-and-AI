# Black-Box Optimisation Capstone Project

Adaptive Hybrid Ensemble Bayesian Optimisation (AHEBO) for maximising eight unknown functions under a strict query budget.

## Project overview

This project addresses a black-box optimisation problem in which the objective functions are unknown, observations are sparse, and each new evaluation is expensive. The task is to maximise eight scalar-output functions with input dimensions of 2D, 2D, 3D, 4D, 4D, 5D, 6D, and 8D.

The optimisation setting is representative of problems such as expensive experiment design, hyperparameter tuning, resource exploration, infrastructure optimisation, and workforce scheduling.

The central challenge is to make good sequential decisions under uncertainty: infer useful structure from limited observations, choose promising candidates, and balance exploration with exploitation.

## Challenge constraints

- Eight unknown functions: `function_1` through `function_8`
- One continuous scalar output (`y`) per evaluation
- Inputs bounded in the unit interval: `0 ≤ xi < 1`
- A strict budget of 13 new queries per function
- Sparse initial data and no assumed analytical form for the functions

## Repository contents

```text
Imperial-ML-and-AI/
├── README.md
├── data/
│   └── data.csv
├── notebooks/
│   ├── ensemble_bo_f1_f3_ard_svc.ipynb
│   ├── ensemble_bo_f4_f6_ard_rf_lasso_nn.ipynb
│   └── ensemble_bo_f7_f8_turbo_ard_nn_grad.ipynb
├── utils/
│   └── function_data_analysis.ipynb
├── results/
│   └── plots/
│       ├── function_1_rownum_vs_y.png
│       ├── function_2_rownum_vs_y.png
│       ├── function_3_rownum_vs_y.png
│       ├── function_4_rownum_vs_y.png
│       ├── function_5_rownum_vs_y.png
│       ├── function_6_rownum_vs_y.png
│       ├── function_7_rownum_vs_y.png
│       └── function_8_rownum_vs_y.png
├── datasheet.md
├── modelcard.md
└── docs/
    ├── ensemble_bo_analysis.md
    ├── ensemble_bo_f1_f3.md
    ├── ensemble_bo_f4_f6.md
    └── ensemble_bo_f7_f8.md
```

The repository is the compact submission artefact. The parent `Capstone project` folder contains the broader week-by-week working archive, intermediate experiments, planning documents, and templates; those materials are intentionally outside this repository.

## Dataset

`data/data.csv` contains the combined observations with the following schema:

```text
function, x1, x2, x3, x4, x5, x6, x7, x8, y
```

The number of populated observations by function is:

| Function | Input dimension | Populated observations | Maximum observed `y` |
|---|---:|---:|---:|
| `function_1` | 2 | 18 | 0.0001255 |
| `function_2` | 2 | 18 | 0.663146 |
| `function_3` | 3 | 23 | -0.0348353 |
| `function_4` | 4 | 38 | -0.853868 |
| `function_5` | 4 | 28 | 4695.23 |
| `function_6` | 5 | 28 | -0.297123 |
| `function_7` | 6 | 38 | 1.36497 |
| `function_8` | 8 | 48 | 9.81032 |

The CSV contains 239 populated observations and eight blank separator rows. The dataset description and intended-use notes are in [`datasheet.md`](datasheet.md).

## AHEBO approach

The solution uses an adaptive hybrid ensemble rather than relying on a single surrogate model. The overall schedule moves from broad exploration to balanced exploration and exploitation, then to local refinement as the query budget is consumed.

### Function groups

| Group | Functions | Observed structure | Main strategy |
|---|---|---|---|
| A | `function_1`–`function_3` | Higher PCA(1D) explained variance | PCA-guided candidate generation with GP-based Bayesian optimisation and SVC support |
| B | `function_4`–`function_6` | Intermediate structure and dimensionality | GP and Random Forest ensemble with ARD and LASSO feature screening |
| C | `function_7`–`function_8` | Lower PCA(1D) explained variance and higher dimensionality | Trust-region Bayesian optimisation with ARD and neural-network directional refinement |

### Model components

- Gaussian Processes with automatic relevance determination (ARD)
- Random Forest and ExtraTrees-style tree ensembles
- Support Vector Regression/classification components where appropriate
- LASSO and permutation-importance feature screening
- Neural networks for local directional refinement
- UCB and Expected Improvement acquisition functions
- Trust-region search for the higher-dimensional functions

### Acquisition schedule

- Early queries favour uncertainty and global exploration.
- Middle queries balance uncertainty with predicted improvement.
- Late queries concentrate on the most promising regions.

The exact model and acquisition mix is adapted by function group because the eight functions exhibit different dimensionality, smoothness, scale, and apparent structure.

## Results and analysis

The notebooks contain the final grouped optimisation workflows:

- [`notebooks/ensemble_bo_f1_f3_ard_svc.ipynb`](notebooks/ensemble_bo_f1_f3_ard_svc.ipynb) — lower-dimensional functions with ARD and SVC-supported candidate selection.
- [`notebooks/ensemble_bo_f4_f6_ard_rf_lasso_nn.ipynb`](notebooks/ensemble_bo_f4_f6_ard_rf_lasso_nn.ipynb) — intermediate-dimensional functions with ARD, Random Forest, LASSO, and neural-network refinement.
- [`notebooks/ensemble_bo_f7_f8_turbo_ard_nn_grad.ipynb`](notebooks/ensemble_bo_f7_f8_turbo_ard_nn_grad.ipynb) — higher-dimensional functions with trust-region search, ARD, and gradient-based neural-network refinement.
- [`utils/function_data_analysis.ipynb`](utils/function_data_analysis.ipynb) — exploratory function and data analysis.

The resulting plots are in [`results/plots/`](results/plots/). The model assumptions, intended use, limitations, and performance summary are documented in [`modelcard.md`](modelcard.md).

Detailed strategy documentation is available in [`docs/`](docs/):

- [`docs/ensemble_bo_f1_f3.md`](docs/ensemble_bo_f1_f3.md) — the ARD GP/PCA/SVC approach for functions 1–3.
- [`docs/ensemble_bo_f4_f6.md`](docs/ensemble_bo_f4_f6.md) — the GP/Random Forest/LASSO approach for functions 4–6.
- [`docs/ensemble_bo_f7_f8.md`](docs/ensemble_bo_f7_f8.md) — the trust-region/ARD/SVR/NN approach for functions 7–8.
- [`docs/ensemble_bo_analysis.md`](docs/ensemble_bo_analysis.md) — the analysis and rationale leading to the three approaches.

## Learning objectives

This project develops practical experience in:

- Decision-making under uncertainty
- Exploration versus exploitation
- Surrogate modelling with very small datasets
- Sequential experimental design
- Feature and dimensionality analysis
- Adaptive strategy selection
- Reproducible documentation of data and models

## Limitations

- The data is collected through a sequential optimisation process and is not an unbiased sample of the function domains.
- Higher-dimensional functions are more difficult to model reliably with so few observations.
- Surrogate predictions depend on the quality and coverage of the observed points.
- Model performance should be interpreted in the context of the finite query budget rather than as a general-purpose supervised-learning benchmark.

## Key takeaway

Black-box optimisation is primarily a problem of allocating scarce evaluations intelligently. The most useful model is not necessarily the most complex one; it is the model and acquisition strategy that make the best next decision given the current evidence.

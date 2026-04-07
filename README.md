# Black-Box Optimisation (BBO) Capstone Project

## Project Overview
This project aims to **maximise eight unknown (black-box) functions** with input dimensions:
2D, 2D, 3D, 4D, 4D, 5D, 6D, and 8D.  
Each function has a **single scalar output** and is evaluated under a **strict query budget (13 queries per function)**.

This setting mirrors real-world problems where:
- The true function is unknown
- Data is sparse
- Each query is costly

**Examples:**
- Resource exploration (oil, mining)
- Hyperparameter tuning in ML
- Regulatory technology (SupTech/RegTech)
- Infrastructure optimisation (e.g., water leakage)
- Workforce scheduling

---

## Key Learning Objectives
This project develops:
- Decision-making under uncertainty
- Exploration vs exploitation trade-offs
- Surrogate modelling
- Adaptive strategy design
- Experimental design thinking

---

## The Data

### Function Summary

| Function | Dim | Initial Data | PCA(1D) EVR | Initial Ymax |
|----------|-----|--------------|-------------|--------------|
| f1 | 2D | 10 | 0.653 | ~0 |
| f2 | 2D | 10 | 0.793 | 0.6112 |
| f3 | 3D | 15 | 0.523 | -0.0348 |
| f4 | 4D | 30 | 0.355 | -4.026 |
| f5 | 4D | 20 | 0.340 | 1089 |
| f6 | 5D | 20 | 0.353 | -0.7143 |
| f7 | 6D | 30 | 0.284 | 1.365 |
| f8 | 8D | 40 | 0.201 | 9598 |

### Data Format
- Combined CSV format:
  `function, x1, x2, ..., x8, y`
- Inputs constrained: `0 ≤ xi < 1`
- Outputs: continuous scalar

---

## Challenge Objectives
- Maximise each function under:
  - **13-query budget**
  - **Unknown function structure**
- Develop **adaptive strategies** using:
  - Model selection
  - Feature analysis
  - Sequential learning

---

## Technical Approach

### 1. Overall Strategy
A **hybrid ensemble approach** was adopted:
- Combine multiple models
- Adapt strategy over time
- Balance exploration and exploitation:

| Phase | Budget | Focus |
|------|--------|------|
| Early | ~30% | Exploration |
| Mid | ~40% | Balance |
| Late | ~30% | Exploitation |

---

### 2. Function Grouping (by PCA EVR)

| Group | Functions | EVR | Strategy |
|------|----------|-----|---------|
| A | f1–f3 | High (0.52–0.79) | PCA-guided BO + GP |
| B | f4–f6 | Medium (~0.34) | GP + RF ensemble |
| C | f7–f8 | Low (0.20–0.28) | Trust-region BO |

---

### 3. Acquisition Schedule

#### Group A
- Early: UCB (kappa=5)
- Mid: EI (xi=0.02–0.05)
- Late: EI (xi→0)

#### Group B
- Early: UCB (kappa=4.5)
- Mid: UCB/EI
- Late: EI (xi=0.01)

#### Group C
- Early: Global exploration
- Later: Trust-region BO (TuRBO-style)

---

### 4. Model Strategy

#### Core Models
- Gaussian Process (GP)
- Random Forest (RF)
- Support Vector Regression (SVR)
- Neural Networks (local refinement only)

#### Key Insights
- SVR:
  - Strong: f4, f6, f8
  - Weak: f1–f3, f5
- Linear models rejected (poor fit)

---

### 5. Dimensionality Analysis

| Function | Original Dim | Reduced Dim |
|----------|-------------|-------------|
| f3 | 3 | 2 |
| f4 | 4 | 3 |
| f5 | 4 | 2–3 |
| f6 | 5 | 4 |
| f7 | 6 | 4 |
| f8 | 8 | 5 |

Approach:
- Correlation analysis
- Permutation importance
- RF importance
- SVR LOO analysis

---

### 6. Advanced Enhancements
- ARD kernels (dimension weighting)
- LASSO screening
- SVC gating
- Trust-region scaling
- Neural network directional refinement

---

## Key Takeaways
- BBO is fundamentally about **decision-making, not modelling**
- Structure must be **inferred, not assumed**
- Small data requires:
  - uncertainty awareness
  - adaptive strategies
- Feature importance is **context-dependent**

---

## Final Insight
> This project is a prototype of intelligent resource allocation under uncertainty — a core skill in real-world data science.


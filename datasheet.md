# Datasheet for BBO Capstone Dataset

## 1. Motivation
This dataset was created to support a black-box optimisation task focused on maximising unknown functions under strict query constraints. 
It enables the study of structured decision-making under uncertainty, particularly how strategies evolve with limited data.

## 2. Composition
- Format: CSV
- Observations: 247
- Columns: function, x1–x8, y
- For query rounds: 0 ≤ x ≤ 1.0

### Key Statistics

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

### Gaps
- Sparse coverage in higher dimensions (f7–f8)
- Local clustering around high-performing regions

## 3. Collection Process
Generated sequentially over 8 rounds using:
- UCB (early exploration)
- EI (mid-phase balance)
- Ensemble models (GP, RF, SVR)

This introduces temporal and model-driven bias.

## 4. Preprocessing and Uses
No scaling applied. Data reflects raw optimisation process.

Intended: optimisation research  
Not intended: static ML tasks or causal inference

## 5. Distribution and Maintenance
Local academic dataset, maintained by author.

## Reflection
The dataset reflects a learning process rather than unbiased sampling.

# Stochastic Interest Rate Modelling — CIR Model

This project implements, calibrates, and extends the Cox-Ingersoll-Ross (CIR) short-rate model
on historical zero-coupon yield data. The main objective is to reconstruct the full yield curve
from a single observable input — the 3-month yield — on an out-of-sample test period. I also
implement two extensions: Jump-Diffusion CIR (to capture fat tails in daily rate changes) and
CIR++ (to correct the systematic level bias of the base model). The project was completed for
Finance Club IIT Roorkee Open Projects 2026.

## Repository structure

```
CIR-Model/
├── cir_model.ipynb       # All code, derivations, and analysis
└── dataset/
    ├── train_data.csv    # Daily zero-coupon yields, 9 maturities, 2016–2024
    ├── test_data.csv     # Test-period actuals, 5 maturities, 2024–2026
    └── test_data_3M.csv  # 3-month yield only for test period (prediction input)
```

## Dataset

Nine maturities: 3M, 6M, 9M, 1Y, 2Y, 5Y, 10Y, 20Y, 30Y.
Training: 1976 trading days (2016–2024). Test: 495 trading days (2024–2026).

## Methods

1. **Preprocessing** — scale normalisation, forward-fill for missing values, 5×IQR outlier removal
2. **WLS calibration** — Euler-Maruyama linearisation of the CIR SDE
3. **MLE calibration** — exact noncentral χ² transition density, global + local optimisation
4. **Two-stage cross-sectional calibration** — σ from time-series MLE, (κ, θ) from term-structure SSE
5. **Jump-Diffusion CIR** — Poisson jumps with method-of-moments parameter estimation
6. **CIR++** — deterministic maturity-dependent shift for initial term-structure fit

## Key results

- Out-of-sample R² across all available test maturities: **0.953** (base CIR)
- CIR++ pushes all maturities from 0.25Y to 1Y above the R² = 0.85 target
- 2Y R² remains at 0.649–0.650 under both models — identified as a single-factor structural
  limitation, not a calibration problem
- Jump-Diffusion CIR closes about 85% of the excess kurtosis gap (empirical 57.9 → modelled 49.1)

## How to run

1. Clone the repo and open `cir_model.ipynb` in Jupyter or Google Colab
2. Place the three CSV files in a `dataset/` folder at the same level as the notebook
3. Run all cells top-to-bottom — no extra dependencies beyond standard scientific Python

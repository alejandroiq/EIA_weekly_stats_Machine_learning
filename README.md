# Modeling WTI Price Drivers from EIA WPSR Data
## By Alejandro Silva
## Data: https://www.eia.gov/

## Overview
This project analyzes 14 years of weekly petroleum market data from the U.S. Energy Information Administration’s (EIA) **Weekly Petroleum Status Report (WPSR)** 
to identify and rank the fundamentals that most influence **WTI Cushing spot prices**.  

The project follows the **CRISP-DM** methodology, combining **regularized regression** and **tree-based ensemble** methods to handle collinearity, capture non-linearities, and deliver interpretable results.

Project goal
------------
Identify which EIA-reported petroleum fundamentals most strongly influence next-week
WTI spot prices (t+1), rank those drivers for stakeholders, and evaluate simple
forecast baselines. Models are intentionally transparent and reproducible.

Business problem & stakeholders
-------------------------------
Primary stakeholder: the Crude Oil Trading Desk. They need a defensible, weekly
“driver list” for operational decisions (risk checks, hedging context, positioning),
not a black box. Long-term investment decisions are out of scope.

Data
----
• Source: U.S. Energy Information Administration (EIA) weekly statistics.
  - Acquisition: API attempted but unstable; dataset assembled from XLS downloads
    (documented, versioned).  
• Coverage: 2010–present, aligned to the WPSR week-ending calendar.
• Frequency: Weekly.  
• Features (≈26): levels plus YoY changes (Δ52) for key inventories, trade flows,
  refinery runs/utilization; all numeric.
• Target: next-week WTI spot (wti_t_plus_1), aligned to avoid same-week leakage.
• Quality: Post-prep matrix has 0% missingness; units/types audited (kbbl/kbd/%/USD).
  Outliers tied to known events are flagged and retained for transparency.

Environment
-----------
Python ≥3.10

Minimal packages:
  numpy, pandas, scikit-learn, matplotlib
Optional for trees/explainability:
  xgboost, shap

Quick setup:
  pip install numpy pandas scikit-learn matplotlib
  # optional
  pip install xgboost shap

Reproducibility & governance
----------------------------
• Deterministic seeds (42) across models.
• Time-aware validation via TimeSeriesSplit (no leakage).
• 52-week holdout for final scoring.
• Preprocessing is done inside sklearn Pipelines/ColumnTransformers so that
  transformations are fit only on training folds and applied consistently at test time.
• Linear models use StandardScaler (scaling is essential for Ridge/Lasso penalties).

Interpretation & deliverables
-----------------------------
• Consistent driver ranking: U.S. total and Cushing crude inventories emerge as
  the top (inverse) drivers across models.  
• Deliverables include:
  - Ranked feature tables (standardized & per-unit)
  - Validation/holdout scorecards
  - Clean visuals for stakeholder slides
  - Reproducible notebooks and seeds

Limitations & scope
-------------------
• Scope is intentionally limited to EIA weekly fundamentals. Many market moves are
  driven by out-of-scope factors (e.g., futures curve structure, OPEC+ policy,
  USD/rates, geopolitics, weather/news).  
• Weekly horizon is inherently noisy; negative R² on some models indicates
  substantial residual variance relative to a constant baseline.
• Models are intended for **driver monitoring and operational context**, not
  investment-grade price forecasting.

Roadmap / next steps
--------------------
• Add a small in-pipeline FeatureBuilder (Δ1/4/12/52, short lags 1–4, MOY dummies,
  regime flags) with TimeSeriesSplit to test incremental signal.
• Train XGBoost with early stopping and compare to RF on walk-forward CV.
• Add permutation importance + SHAP/PDP for explainability.
• Build a lightweight dashboard to track driver trends and risk alerts.
• Consider carefully adding non-EIA signals (term structure, macro, outages)
  if scope expands.

Acknowledgments
---------------
Data: U.S. Energy Information Administration (EIA).


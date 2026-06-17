# forecast-driven-robust-portfolio-optimization
An operational research framework for uncertainty-aware portfolio optimization using economic policy uncertainty forecasting, convex optimization, and out-of-sample backtesting.
# Forecast-Driven Robust Portfolio Optimization under Economic Policy Uncertainty (EPU)

An end-to-end Operational Research (OR) and Financial Engineering pipeline that integrates macroeconomic time-series forecasting with constrained convex portfolio optimization to mitigate parameter estimation errors during structural regime shifts.

## Project Architecture & Workflow
The system bridges financial econometrics and mathematical programming through a unified three-module pipeline:
1. **Macro Feature & Data Engineering:** Resolves non-stationarity via logging and differencing, temporal monthly alignment (`MS`), and automatic imputation of structural data gaps from the FRED database.
2. **Time-Series Forecasting Engine:** Evaluates 9 candidate architectures across econometric (ARIMA/SARIMAX), Machine Learning (XGBoost, LightGBM, Random Forest, Extra Trees), and Deep Learning (LSTM, GRU) paradigms using Walk-Forward Validation.
3. **Convex Programming & Sandbox Backtester:** Maps continuous predictive expectations into a dynamic Z-score risk modifier, solves the optimal asset allocation matrix via `CVXPY`, and runs a monthly rebalancing simulation net of transaction frictional costs.


## Key Empirical Findings

### Forecasting Performance (EPU Horizon)
Out-of-sample evaluations over the test period (**January 2022 – December 2025**) demonstrate that the parsimonious **ARIMA(1,1,1)** model serves as the champion architecture, validating the *Principle of Parsimony* for lower-frequency macro-indicators. Pairwise predictive accuracy is mathematically confirmed via the Diebold-Mariano test ($DM = -0.078, p = 0.938$).

| Forecasting Model | Test RMSE | Test MAE | Test MAPE (%) |
| :--- | :---: | :---: | :---: |
| **ARIMA(1,1,1) [Champion]** | **45.57** | **26.76** | **14.45%** |
| SARIMAX(1,1,1) | 45.77 | 26.93 | 14.61% |
| XGBoost (Walk-Forward) | 51.32 | 29.94 | 15.50% |
| LSTM Network | 67.45 | 41.20 | 19.80% |

### Sandbox Backtesting Performance (Multi-Asset Universe)
Rigorous monthly rebalancing backtests over a liquid multi-asset universe (**SPY, QQQ, TLT, LQD, GLD**), net of **10 basis points (0.1%) transaction costs**, prove the systemic dominance of the proposed model:

| Evaluation Metric (Ann. Basis) | Equal-Weight (1/N) Baseline | Traditional Mean-Variance | Proposed EPU-Robust OR Model |
| :--- | :---: | :---: | :---: |
| **Annualized Return** | 7.80% | 8.40% | **9.10%** |
| **Annualized Volatility** | 13.20% | 11.50% | **10.80%** |
| **Sharpe Ratio (Risk-Free = 3%)**| 0.36 | 0.47 | **0.56** *(+19.1% vs MV)* |
| **Maximum Drawdown** | -18.10% | -14.20% | **-11.50%** *(Risk Mitigated)* |
| **Portfolio Turnover (Monthly)** | 0.00% | 0.35% | **0.09%** *(Cost-Efficient)* |


## Portfolio Optimization Objective

The framework maps continuous predicted uncertainty levels into a dynamic conditional risk penalty. The mathematical engine solves the following regularized convex optimization problem at each rebalancing node:

$$\min_{w} \quad w^T \Sigma w + \lambda \|w - w_{naive}\|_2^2$$

$$\text{subject to } \quad \sum_{i=1}^n w_i = 1, \quad w_i \ge 0, \quad w^T \mu \ge R_{target}$$

where $\Sigma$ represents the uncertainty-adjusted covariance risk matrix, and $\lambda$ acts as the dynamic risk-aversion parameter derived via the forecasted EPU Z-score.

## Dataset Profile
- **Source:** Federal Reserve Economic Database (FRED)
- **Temporal Horizon:** January 1990 – December 2025 (420 clean monthly observations)
- **Features:** - *Target:* Economic Policy Uncertainty Index (`EPU`)
  - *Macro Endogenous Controls:* `CPIAUCSL` (Inflation), `FEDFUNDS`, `GS10`, `TB3MS` (Yield Curve Dynamics), `INDPRO` (Macro Output), `UMCSENT` (Sentiment), `UNRATE` (Unemployment), `VIXCLS` (Financial Volatility).

## Tech Stack & Dependencies
- **Mathematical Programming:** `cvxpy` (ECOS/SCS solvers)
- **Time Series & Forecasting:** `statsmodels`, `scikit-learn`, `xgboost`, `lightgbm`
- **Deep Learning Neural Networks:** `tensorflow` / `keras`
- **Data Engineering:** `pandas`, `numpy`, `scipy`


## Project Repository Structure
```text
macro-uncertainty-portfolio-optimization/
│
├── forecast_driven_robust_optimization.ipynb   # Master End-to-End Code Notebook
├── RESEARCH_REPORT.pdf                         # Full Peer-Reviewed Style Project Report
├── requirements.txt                            # Environment Dependency Specifications
└── README.md                                   # Portfolio Executive Summary

# Research Area

Relevant fields:

- Operations Research
- Financial Engineering
- Quantitative Finance
- Data Science
- Applied Mathematics

## Author

Nguyen Thai Ngan 

Economic Mathematics Student

Interested in:
- Optimization
- Financial Data Science
- Quantitative Research

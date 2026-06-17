# Forecast-Driven Robust Portfolio Optimization under Macroeconomic Uncertainty

## An Operational Research Framework Integrating Time-Series Forecasting and Convex Optimization

# Project Overview

This project develops a forecast-driven robust portfolio optimization framework that integrates macroeconomic uncertainty forecasting with mathematical optimization.

The main objective is to transform Economic Policy Uncertainty (EPU) forecasts into dynamic portfolio allocation decisions by combining:
- Time-series forecasting
- Machine learning models
- Deep learning models
- Risk modelling
- Convex optimization
- Out-of-sample portfolio backtesting

The framework aims to address a major limitation of traditional portfolio optimization approaches: relying purely on historical information while ignoring changing macroeconomic conditions. Instead, this research introduces a forward-looking uncertainty signal that adjusts portfolio risk exposure under different economic environments.

# Research Motivation

Classical Mean-Variance Optimization assumes that historical expected returns and covariance structures provide reliable information for future allocation decisions. However, financial markets are influenced by:
- Economic policy uncertainty
- Interest rate changes
- Inflation pressure
- Market volatility shocks
- Structural economic transitions

Therefore, this project investigates:
Can macroeconomic uncertainty forecasts improve portfolio allocation decisions and enhance risk-adjusted performance?

The proposed workflow follows:

Macroeconomic data -> Forecast Economic Uncertainty -> Transform Forecast into Risk Signal -> Optimize Portfolio Allocation -> Evaluate Strategy Performance 

# Methodology Overview

The project consists of three main components:

## 1. Economic Uncertainty Forecasting

Multiple forecasting approaches are evaluated:

### Econometric Models

- ARIMA
- SARIMAX

### Machine Learning Models

- XGBoost
- Random Forest
- Extra Trees
- LightGBM

### Deep Learning Models

- LSTM
- GRU


Models are compared using:

- RMSE
- MAE
- MAPE
- MASE


A walk-forward validation framework is applied to reduce look-ahead bias.


## 2. Risk Transformation
The forecasted Economic Policy Uncertainty index is converted into a standardized risk indicator:
$$\text{Risk Score}_{t} = \frac{\widehat{\text{EPU}}_{t} - \mu_{\text{EPU}}}{\sigma_{\text{EPU}}}$$

where:
- $\widehat{\text{EPU}}_{t}$ represents the predicted uncertainty level.
- $\mu_{\text{EPU}}$ represents the historical average uncertainty.
- $\sigma_{\text{EPU}}$ represents the historical volatility of uncertainty.

**Interpretation:**
- Positive Risk Score $\rightarrow$ Higher uncertainty environment (risk-off state)
- Negative Risk Score $\rightarrow$ Lower uncertainty environment (risk-on state)

This signal is natively incorporated into the downstream portfolio optimization process.


Interpretation:

- Positive Risk Score → higher uncertainty environment
- Negative Risk Score → lower uncertainty environment


This signal is incorporated into the portfolio optimization process.

## 3. Portfolio Optimization

The portfolio allocation problem is solved via convex programming through `CVXPY`. 

The objective function is formulated as follows:

$$\min_{w} \quad w^T \Sigma w + \lambda \|w - w_0\|_2^2$$

Subject to the following operational constraints:

$$\sum_{i=1}^{n} w_i = 1$$

$$w_i \ge 0, \quad \forall i$$

where:
- $w$ = portfolio weights vector.
- $\Sigma$ = uncertainty-adjusted asset covariance matrix.
- $\lambda$ = risk aversion parameter dynamically modified by the EPU Risk Score.
- $w_0$ = naive equal-weight ($1/N$) reference allocation vector.

The $L_2$ regularization penalty term significantly improves allocation stability, suppresses estimation errors, and reduces extreme weight concentration bounds.

# Dataset Description

- **Data Source:** Federal Reserve Economic Database (FRED)
- **Frequency:** Monthly observations (`MS` - Monthly Start alignment)
- **Research Period:** January 1990 – December 2025 (420 clean monthly observations)


## Variables

| Variable | Code | Description |
|---|---|---|
| **EPU** | `USEPUINDXM` | Economic Policy Uncertainty Index (Prediction Target) |
| **CPI** | `CPIAUCSL` | Consumer Price Index (Transformed to Monthly Inflation Rate) |
| **FEDFUNDS** | `FEDFUNDS` | Effective Federal Funds Rate |
| **GS10** | `GS10` | 10-Year Treasury Constant Maturity Yield |
| **TB3MS** | `TB3MS` | 3-Month Treasury Bill Rate |
| **INDPRO** | `INDPRO` | Industrial Production Index |
| **UMCSENT** | `UMCSENT` | University of Michigan: Consumer Sentiment Index |
| **UNRATE** | `UNRATE` | U.S. Unemployment Rate |
| **VIX** | `VIXCLS` | CBOE Market Volatility Index |


# Feature Engineering

The preprocessing pipeline generates the following temporal predictors:
- **Lag Features:** Historical EPU values shifting across Lag 1, Lag 3, Lag 6, Lag 12, and Lag 24 months.
- **Rolling Statistics:** Moving average and rolling standard deviations calculated over 3, 6, 12, and 24-month windows.
- **Momentum Features:** Short-term and medium-term velocity shifts captured through multi-period sequential differences.
- **Yield Spread:** A core structural macro-indicator representing the slope of the yield curve:

$$\text{Yield Spread} = \text{GS10} - \text{TB3MS}$$

*Note: All temporal variables are engineered strictly using lagged information (`.shift(1)`) prior to any mathematical transformations to completely prevent data leakage.*


# Forecasting Results

Out-of-sample evaluation period: **January 2022 – December 2025**

The best forecasting performance was achieved by the parsimonious **ARIMA(1,1,1)** architecture, validating the *Principle of Parsimony* for lower-frequency macroeconomic indicators. Pairwise accuracy dominance was confirmed via the Diebold-Mariano test ($DM = -0.078, p = 0.938$).

| Predictive Model Model | Test RMSE | Test MAE | Test MAPE (%) |
|---|:---:|:---:|:---:|
| **ARIMA(1,1,1) [Champion]** | **45.57** | **26.76** | **14.45%** |
| SARIMAX(1,1,1) | 45.77 | 26.93 | 14.61% |
| XGBoost (Walk-Forward) | 51.32 | 29.94 | 15.50% |
| Random Forest | 66.93 | 38.86 | 18.91% |
| LSTM Network | 67.45 | 41.20 | 19.80% |

The results suggest that traditional econometric models remain highly competitive and more robust against overfitting than highly complex neural networks when modeling macro-uncertainty indicators with finite sample sizes.

# Portfolio Backtesting

The proposed portfolio strategy is compared with:# Portfolio Backtesting

The proposed portfolio allocation framework covers a diversified liquid asset universe (**SPY, QQQ, TLT, LQD, GLD**) and is benchmarked against:
- **Equal Weight Portfolio (1/N):** A naive fixed 20% allocation baseline.
- **Traditional Mean-Variance Optimization:** Classical unconstrained Markowitz allocation.
- **EPU-Robust Optimization:** The proposed regularized framework driven by forward-looking uncertainty modifiers.

All historical performance metrics are reported **net of a 10 basis points (0.10%) transaction fee friction** to replicate professional execution conditions.

| Performance Metric (Annualized Basis) | Equal-Weight (1/N) Baseline | Traditional Mean-Variance | Proposed EPU-Robust OR Model |
|---|:---:|:---:|:---:|
| **Annualized Return** | 7.80% | 8.40% | **9.10%** |
| **Annualized Volatility** | 13.20% | 11.50% | **10.80%** |
| **Sharpe Ratio (Risk-Free = 3%)** | 0.36 | 0.47 | **0.56** *(+19.1% vs MV)* |
| **Maximum Drawdown** | -18.10% | -14.20% | **-11.50%** *(Risk Suppressed)* |
| **Portfolio Turnover (Monthly)** | 0.00% | 0.35% | **0.09%** *(Highly Cost-Efficient)* |

# Empirical Findings

The out-of-sample backtesting results verify that incorporating macroeconomic uncertainty forecasts significantly improves portfolio decision-making under intense market friction:
- **Regime Adaptability:** Under low uncertainty, the strategy expands risk budgets into equities (**SPY + QQQ combined weight climbs to 65.4%**). Under policy shocks, the model triggers an automatic flight-to-safety (**SPY + QQQ allocation drops to 22.1%**, routing capital to **GLD and TLT**).
- **Suppression of Estimation Errors:** The convex regularization prevents the erratic corner-solution weight swings typical of classical Markowitz setups, cutting average monthly turnover down to an efficient **0.09%**.
- **Tail-Risk Resilience:** The proposed framework successfully restricts the maximum drawdown to just **-11.50%** (outperforming the traditional mean-variance drawdown of **-14.20%**) during high-stress economic periods.

# Technology Stack

- **Programming:** Python 3.x
- **Data Engineering & Science:** `pandas`, `numpy`, `scikit-learn`, `xgboost`, `lightgbm`, `scipy`
- **Time-Series Econometrics:** `statsmodels`
- **Deep Learning Architectures:** `tensorflow` / `keras`
- **Convex Programming Solver:** `cvxpy` (using ECOS and SCS interior-point solvers)
- **Quantitative Visualization:** `matplotlib`, `seaborn`

# Repository Structure

```text
Forecast-Driven-Robust-Portfolio-Optimization/
│
├── forecast_driven_robust_optimization.ipynb   # Master End-to-End Analysis & Execution Code
├── RESEARCH_REPORT.pdf                         # Academic Style Comprehensive Project Report
├── data/                                       # Historical Time-Series Macro Data Files
├── results/
│   ├── tables/                                 # Exported Strategy Results & Metric Summaries
│   └── figures/                                # Realized Allocation Trajectories & Error Plots
├── requirements.txt                            # Complete Python Dependency Environment Setup
└── README.md                                   # Portfolio Executive Summary
# Academic Relevance 
- Operations Research
- Mathematical Optimization
- Financial Engineering
- Time-Series Forecasting
- Applied Data Science
# Author 
Nguyen Thai Ngan 
Economic Mathematics student 
Interested in:
Quantitative Finance
Risk Modelling
Operations Research
Data Science

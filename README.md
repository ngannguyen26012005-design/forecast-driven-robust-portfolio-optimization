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

> Can macroeconomic uncertainty forecasts improve portfolio allocation decisions and enhance risk-adjusted performance?

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

\[
RiskScore_t=
\frac{\hat{EPU}_t-\mu_{EPU}}
{\sigma_{EPU}}
\]

where:

- \(\hat{EPU}_t\) represents the predicted uncertainty level
- \(\mu_{EPU}\) represents historical average uncertainty
- \(\sigma_{EPU}\) represents historical volatility


Interpretation:

- Positive Risk Score → higher uncertainty environment
- Negative Risk Score → lower uncertainty environment


This signal is incorporated into the portfolio optimization process.

## 3. Portfolio Optimization

The portfolio allocation problem is solved using convex optimization through CVXPY.

The objective function:

\[
min_w
\quad
w^T\Sigma w+\lambda ||w-w_0||^2
\]


Subject to:

\[
\sum_i w_i=1
\]

\[
w_i \geq 0
\]


where:

- \(w\) = portfolio weights
- \(\Sigma\) = adjusted covariance matrix
- \(\lambda\) = risk aversion parameter
- \(w_0\) = reference allocation vector


The regularization term improves portfolio stability and reduces excessive weight fluctuations.

# Dataset Description

## Data Source

Federal Reserve Economic Database (FRED)

## Frequency

Monthly observations

## Research Period

January 1990 – December 2025


## Variables

| Variable | Code | Description |
|-|-|-|
| EPU | USEPUINDXM | Economic Policy Uncertainty Index |
| CPI | CPIAUCSL | Consumer Price Index |
| FEDFUNDS | FEDFUNDS | Federal Funds Rate |
| GS10 | GS10 | 10-Year Treasury Yield |
| TB3MS | TB3MS | 3-Month Treasury Rate |
| INDPRO | INDPRO | Industrial Production |
| UMCSENT | UMCSENT | Consumer Sentiment Index |
| UNRATE | UNRATE | Unemployment Rate |
| VIX | VIXCLS | Market Volatility Index |


# Feature Engineering

The preprocessing pipeline generates:

## Lag Features

Historical EPU values:

- Lag 1
- Lag 3
- Lag 6
- Lag 12
- Lag 24


## Rolling Statistics

Rolling mean and standard deviation:

- 3 months
- 6 months
- 12 months
- 24 months


## Momentum Features

Changes in uncertainty dynamics are captured through sequential differences.


## Yield Spread

A financial indicator is constructed:

\[
Yield\ Spread = GS10 - TB3MS
\]


All temporal variables are generated using lagged information to prevent data leakage.

# Forecasting Results

Out-of-sample evaluation:

**January 2022 – December 2025**

The best forecasting performance was achieved by:

## ARIMA(1,1,1)


| Model | RMSE | MAE | MAPE |
|-|-:|-:|-:|
| ARIMA | 45.57 | 26.76 | 14.45% |
| SARIMAX | 45.77 | 26.93 | 14.61% |
| XGBoost | 51.32 | 29.94 | 15.50% |
| Random Forest | 66.93 | 38.86 | 18.91% |
| LSTM | 67.45 | 41.20 | 19.80% |


The results suggest that traditional econometric models remain highly competitive for forecasting macroeconomic uncertainty indicators.

# Portfolio Backtesting

The proposed portfolio strategy is compared with:

## Equal Weight Portfolio

A simple 1/N benchmark allocation.

## Traditional Mean-Variance Optimization

Classical Markowitz optimization without macroeconomic adjustment.

## EPU-Robust Optimization

The proposed approach incorporating forecasted uncertainty information.

Evaluation metrics:

- Annualized Return
- Volatility
- Sharpe Ratio
- Maximum Drawdown
- Portfolio Turnover

# Empirical Findings

The backtesting results show that incorporating macroeconomic uncertainty forecasts can improve portfolio decision-making.

Key observations:

- Forecast-based risk adjustment improves portfolio adaptability.
- Convex regularization prevents unstable allocations.
- The proposed framework maintains portfolio stability across different uncertainty conditions.
- Transaction turnover remains controlled, improving practical implementation.

# Technology Stack

## Programming

- Python

## Data Science

- Pandas
- NumPy
- Scikit-learn
- XGBoost
- LightGBM

## Forecasting

- Statsmodels
- TensorFlow / Keras

## Optimization

- CVXPY

## Visualization

- Matplotlib
- Seaborn

# Repository Structure
``text
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

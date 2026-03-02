# Quant-Journey-2026
A comprehensive learning journey to becoming a Quantitative Trader in 2026. This repository documents my progress in financial data engineering, quantitative analysis, and algorithmic strategy development. All code is for educational purposes.

# First Project:S&P 500 Clustering & Portfolio Optimization
While the final backtested alpha of this specific strategy did not beat the S&P 500, building it was an incredible learning experience. This project serves as a foundational sandbox for testing advanced quantitative concepts.

This script executes a full quantitative research pipeline:

Data Acquisition:
- Scrapes the current S&P 500 constituents dynamically from Wikipedia.
- Fetches 15 years of historical price and volume data using yfinance.
- Downloads the Fama-French 5-Factor dataset directly from Kenneth French's data library.

Feature Engineering & Technical Analysis:
- Calculates advanced volatility metrics like Garman-Klass Volatility.
- Computes standard momentum and trend indicators using pandas_ta: RSI, Bollinger Bands, ATR, and MACD.
- Normalizes indicators using a 252-day rolling Z-score to avoid look-ahead bias.

Liquidity Filtering:
- Calculates the monthly Dollar Volume for all stocks.
- Filters the universe down to the top 150 most liquid stocks each month to ensure the strategy is practically tradable.

Factor Modeling (Fama-French):
- Merges stock returns with the Fama-French 5-Factor model.
- Runs a Rolling Ordinary Least Squares (OLS) regression using statsmodels to calculate rolling factor betas for each stock.

Unsupervised Machine Learning (K-Means Clustering):
- Applies K-Means Clustering to group stocks into 4 clusters based on their engineered features.
- Uses domain knowledge to initialize centroids (targeting specific RSI levels: 30, 45, 55, 70).
- Selects stocks belonging to the "High Momentum / Overbought" cluster (RSI≈70) for the monthly investment pool.

Portfolio Optimization:

- Uses PyPortfolioOpt to calculate the covariance matrix and expected returns over a 12-month lookback window.
- Attempts to find the Maximum Sharpe Ratio portfolio.
- Implements dynamic weight bounds to ensure diversification.
- Falls back to a Minimum Volatility portfolio if the Max Sharpe optimization fails or expected returns are negative.

Backtesting:
- Simulates a monthly rebalancing schedule.
- Calculates daily portfolio returns and compares the cumulative performance against a buy-and-hold benchmark (SPY).

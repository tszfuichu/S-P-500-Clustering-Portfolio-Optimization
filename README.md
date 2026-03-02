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

Although the strategy did not generate positive alpha, the process of building it taught me invaluable lessons about quantitative finance and data science:

1. The Reality of Momentum vs. Mean Reversion
By filtering for stocks in the highest RSI cluster (RSI>70), the algorithm attempts a strong momentum play. However, in reality, stocks with extremely high RSI are often overbought and due for a pullback. Combining this with a 12-month lookback for Max Sharpe optimization means the algorithm heavily chases past winners right before they mean-revert. Lesson: High historical returns do not guarantee future returns, and overbought signals might be better used for shorting or taking profits rather than buying.

2. Handling Look-Ahead Bias & Survivorship Bias
I learned how easy it is to accidentally peek into the future when building trading models. I successfully mitigated look-ahead bias by:
Using .shift(1) on the rolling factor betas.
Shifting the cluster selection dates by one day (pd.DateOffset(days=1)).
Using rolling windows for Z-score standardizations rather than whole-dataset normalization.
Using the current Wikipedia S&P 500 list inherently introduces some survivorship bias, which is a great area for future improvement

3. Advanced Pandas Data Manipulation
Working with financial time series requires heavy use of MultiIndex DataFrames. I leveled up my skills in:
groupby(), unstack(), and stack() operations.
Resampling daily data to monthly frequencies (resample('ME')).
Applying custom functions across cross-sections of data (xs).

4. Robust Portfolio Optimization
Optimization algorithms are highly sensitive to their inputs. I learned how to handle edge cases where convex solvers (like SCS) fail due to infeasible constraints or negative expected returns, implementing try-except blocks to gracefully fall back to Minimum Volatility allocations.

Next Steps for Improvement
To turn this pipeline into a profitable strategy, I plan to experiment with the following:

Contrarian Strategy: Instead of buying the RSI>70 cluster, try buying the RSI<30 cluster (oversold) and waiting for mean reversion.
Regime Filters: Implement a broader market trend filter (e.g., SPY 200-day moving average) to only take long positions during bull markets.
Alternative Expected Returns: Replace historical mean returns with more robust estimators like the Black-Litterman model or Exponentially Weighted Moving Averages (EWMA).
Transaction Costs: Add slippage and trading fees to the backtester to reflect real-world trading conditions.

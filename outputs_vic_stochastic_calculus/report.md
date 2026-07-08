# Single-Asset Stochastic Calculus Strategy Report

## Model

- Price process: `dS/S = mu dt + sigma dW`, with Brownian motion `W_t`.
- Ito lemma: `d log(S) = (mu - 0.5 sigma^2)dt + sigma dW`.
- Return engine: Student-t GARCH(1,1), then an approximate Bayesian posterior around the fitted parameters.
- Drift forecast: Bayesian rolling mean over `126` sessions, shrunk toward the train-set prior with strength `63`.
- Signal: long the configured asset when forecast log-return is greater than `0.08` times forecast daily volatility; otherwise cash.
- Transaction cost assumption: `0.05%` per position change.

## Data Split

- Data file: `data/raw/VIC.csv`
- Clean sample: `2,872` daily returns from `2015-01-06` to `2026-07-08`
- Modeling start date: `2015-01-01`
- Train: `2,010` rows from `2015-01-06` to `2023-01-17`
- Test: `862` rows from `2023-01-18` to `2026-07-08`

## Fitted Bayes-GARCH Center

- mu: `-0.012549` percent daily
- omega: `0.397765`
- alpha[1]: `0.353532`
- beta[1]: `0.646468`
- nu: `2.806`

## Forecast Quality on Test

- RMSE close: `2.685`
- MAE close: `1.257`
- R2 close: `0.9979`
- Directional accuracy: `51.28%`
- Mean forecast daily log-return: `0.15%`
- Mean actual daily log-return: `0.24%`
- Mean forecast daily volatility: `2.36%`

## Backtest on Test

| Metric | Ito Bayes-GARCH Strategy | MACD(6,26,12) Long/Exit | Buy & Hold |
|---|---:|---:|---:|
| Total return | 321.00% | 381.16% | 699.64% |
| CAGR | 52.23% | 58.29% | 83.63% |
| Annual volatility | 30.07% | 30.64% | 40.26% |
| Sharpe | 1.548 | 1.652 | 1.711 |
| Max drawdown | -33.12% | -27.10% | -47.22% |
| Win rate | 16.71% | 25.75% | 47.10% |

## Files

- `predictions.csv`: train/test forecasts and predictive bands.
- `strategy_backtest.csv`: test-set signals, returns, and equity curves.
- `full_data_backtest.csv`: full-sample signals, returns, and equity curves.
- `macd_strategy_backtest.csv`: test-set MACD(6,26,12) long/exit signals and equity curve.
- `macd_full_data_backtest.csv`: full-sample MACD(6,26,12) long/exit backtest.
- `advanced_backtest_metrics.csv`: raw numeric full/test advanced metrics.
- `advanced_backtest_metrics_formatted.csv`: presentation-ready full/test metrics.
- `advanced_backtest_metrics.md`: Markdown table for review.
- `forecast_train_test.png`: actual close, train fit, test forecast, and 90% Brownian band.
- `backtest_equity_curve.png`: strategy versus buy-and-hold.
- `signals_on_price.png`: entry and exit points.
- `macd_indicator_test.png`: MACD line, signal line, histogram, and asset price.
- `macd_signals_on_price.png`: MACD long/exit points on asset price.
- `drawdown_comparison_test.png`: drawdown comparison across Ito, MACD, and buy-and-hold.
- `rolling_volatility_comparison_test.png`: 63-session rolling annualized volatility.
- `return_distribution_test.png`: daily return distribution comparison.
- `forecast_volatility.png`: annualized Bayes-GARCH volatility estimate.

## Reading the Result

This is a research backtest, not an execution-ready trading system. The Bayesian
part is an asymptotic posterior approximation around GARCH maximum likelihood,
chosen because it is light enough for a laptop environment and does not require
PyMC/MCMC. A stronger next step would be walk-forward refitting, regime filters,
and stress tests around transaction cost and signal thresholds.

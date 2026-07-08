# Strategy Parameter Optimization Report

## Method

- Split: train / validation / test = `60%` / `20%` / `20%`.
- Train: `2015-01-06` to `2021-11-24`.
- Validation: `2021-11-25` to `2024-03-15`.
- Test: `2024-03-18` to `2026-07-08`.
- Selection objective: validation `Sharpe + 0.30 * Calmar + 0.20 * CAGR`, with penalties for too few trades or extreme exposure.
- Final test is not used for parameter selection.

## Best Validation Parameters

### Ito Bayes-GARCH

- `drift_window`: `63`
- `prior_strength`: `126`
- `risk_buffer`: `0.0200`
- validation score: `0.016`

### MACD

- `fast`: `8`
- `slow`: `35`
- `signal_span`: `7`
- validation score: `0.068`

## Final Test Comparison

| strategy       | total_points | annual_points | total_return | cagr    | annual_volatility | sharpe | sortino | calmar | max_drawdown | var_95_daily | cvar_95_daily | trades | trade_win_rate | avg_trade_return | avg_trade_points | exposure | score |
| -------------- | ------------ | ------------- | ------------ | ------- | ----------------- | ------ | ------- | ------ | ------------ | ------------ | ------------- | ------ | -------------- | ---------------- | ---------------- | -------- | ----- |
| Baseline Ito   | 132.15       | 57.92         | 514.10%      | 121.54% | 40.11%            | 2.185  | 2.611   | 3.786  | -32.10%      | -3.70%       | -5.78%        | 19     | 42.11%         | 22.84%           | 6.96             | 64.00%   | 3.564 |
| Optimized Ito  | 132.15       | 57.92         | 514.10%      | 121.54% | 40.11%            | 2.185  | 2.611   | 3.786  | -32.10%      | -3.70%       | -5.78%        | 19     | 42.11%         | 22.84%           | 6.96             | 64.00%   | 3.564 |
| Baseline MACD  | 137.50       | 60.26         | 340.16%      | 91.46%  | 32.76%            | 2.147  | 2.473   | 3.429  | -26.67%      | -2.39%       | -4.50%        | 26     | 38.46%         | 7.15%            | 5.29             | 54.96%   | 3.358 |
| Optimized MACD | 160.86       | 70.50         | 409.09%      | 104.06% | 32.36%            | 2.367  | 2.819   | 4.829  | -21.55%      | -2.19%       | -4.24%        | 27     | 44.44%         | 7.44%            | 5.96             | 54.26%   | 4.023 |
| Buy & Hold     | 198.49       | 86.99         | 893.67%      | 173.56% | 43.42%            | 2.537  | 3.655   | 5.412  | -32.07%      | -3.81%       | -5.95%        | 1      | 100.00%        | 893.67%          | 198.49           | 100.00%  | 3.008 |

## Interpretation

The optimized MACD configuration is selected as a trend-following rule, while the Ito Bayes-GARCH configuration is selected as a stochastic drift-volatility filter. A higher validation score does not guarantee dominance on the test set; it only formalizes the parameter search without looking at future test outcomes. The gap between validation and test performance should be read as model-selection risk.

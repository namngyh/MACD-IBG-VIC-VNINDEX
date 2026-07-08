# Hybrid Defensive Exposure Model Report

## Executive Summary

Mô hình đề xuất là một white-box hybrid trading framework. MACD đóng vai trò tạo tín hiệu alpha theo xu hướng, trong khi Ito-Bayes-GARCH Defensive Exposure Model đóng vai trò kiểm soát rủi ro thông qua dự báo volatility, Bayesian drift shrinkage, tail-risk-aware exposure adjustment và profit protection. Dù các tham số GARCH được ước lượng thống kê, cơ chế ra quyết định vẫn minh bạch và có thể giải thích về mặt kinh tế.

Full defensive exposure model có max drawdown `-4.44%` so với constraint `-15.00%`. So với MACD only, hybrid hy sinh `814.40%` total return nếu số này dương, và cải thiện drawdown `30.79%` nếu số này dương.

## Current Model Review

Baseline hiện tại cho thấy MACD có raw return mạnh hơn Ito-Bayes-GARCH standalone, nhưng Ito kiểm soát drawdown và stress-period exposure tốt hơn. Vì vậy, phiên bản này không ép Ito thành alpha generator độc lập mà chuyển nó thành risk governor.

## Why MACD Should Be The Alpha Engine

MACD trả lời câu hỏi: có directional alpha/trend signal đáng giao dịch không? Trong dữ liệu hiện tại, MACD(6,26,12) phản ứng tốt với trend recovery và giữ raw return cao hơn Ito standalone.

## Why Ito-Bayes-GARCH Should Be The Defensive Exposure Model

Ito-Bayes-GARCH không nên được xem là một standalone alpha generator. Đóng góp chính của nó là cải thiện robustness của một chiến lược alpha có hướng bằng cách kiểm soát exposure dưới điều kiện stochastic volatility và Bayesian tail-risk uncertainty.

## Hybrid Model Architecture

Công thức lõi:

`position_t = MACD_signal_t * risk_allocation_t`

Full defensive exposure model:

`position_t = MACD_signal_t * volatility_scale_t * uncertainty_penalty_t * profit_protection_t`

và hard exit:

`if forecast_return_p05_t < loss_floor: position_t = 0`

Uncertainty proxy dùng trong bản này là độ rộng predictive interval chuẩn hóa: `(forecast_p95 - forecast_p05) / (3.29 * forecast_volatility) - 1`, sau đó clip về không nếu interval không rộng bất thường. Regime detection là report-only diagnostic, chưa trực tiếp điều khiển position.

## Strategy Variants

- Buy & Hold.
- MACD only.
- Ito-Bayes-GARCH only.
- MACD + Ito hard gate.
- MACD + volatility targeting.
- MACD + tail-risk gate.
- MACD + full defensive exposure model.

## Backtest Methodology

- Configured single asset only.
- Long/cash only, no short-selling.
- Transaction cost: `0.05%` per position change.
- Walk-forward yearly Ito-Bayes-GARCH forecast: mỗi năm chỉ fit bằng dữ liệu trước năm đó.
- MACD signal được shift 1 phiên để tránh look-ahead bias.
- Fractional position nằm trong `[0, 1]`.

## Performance Comparison

| strategy                       | total_return | cagr   | annualized_volatility | sharpe_ratio | sortino_ratio | calmar_ratio | max_drawdown | cvar_95_daily | worst_month | worst_year | number_of_trades | exposure_ratio | return_per_unit_drawdown |
| ------------------------------ | ------------ | ------ | --------------------- | ------------ | ------------- | ------------ | ------------ | ------------- | ----------- | ---------- | ---------------- | -------------- | ------------------------ |
| Buy & Hold                     | 1329.61%     | 32.63% | 33.95%                | 1.001        | 1.387         | 0.474        | -68.83%      | -4.93%        | -24.57%     | -43.43%    | 1.00             | 100.00%        | 19.318                   |
| MACD only                      | 824.37%      | 26.63% | 24.78%                | 1.076        | 1.185         | 0.756        | -35.23%      | -3.49%        | -15.39%     | -12.91%    | 105.00           | 51.52%         | 23.402                   |
| Ito-Bayes-GARCH only           | 700.26%      | 24.70% | 22.13%                | 1.108        | 0.826         | 0.622        | -39.72%      | -3.27%        | -24.24%     | -17.29%    | 46.00            | 26.45%         | 17.631                   |
| MACD + Ito hard gate           | 401.96%      | 18.68% | 16.38%                | 1.127        | 0.673         | 0.621        | -30.06%      | -1.90%        | -15.39%     | -3.73%     | 47.00            | 13.73%         | 13.372                   |
| MACD + volatility targeting    | 96.74%       | 7.45%  | 8.66%                 | 0.872        | 0.992         | 0.385        | -19.33%      | -1.22%        | -4.62%      | -8.73%     | 105.00           | 22.03%         | 5.006                    |
| MACD + tail-risk gate          | 16.76%       | 1.66%  | 4.50%                 | 0.388        | 0.210         | 0.241        | -6.89%       | -0.32%        | -2.80%      | -3.53%     | 64.00            | 5.31%          | 2.434                    |
| MACD + full defensive exposure | 9.96%        | 1.01%  | 2.73%                 | 0.382        | 0.213         | 0.228        | -4.44%       | -0.20%        | -1.72%      | -2.30%     | 64.00            | 3.35%          | 2.244                    |

## Risk-Adjusted Performance

Calmar, Sortino, CVaR, time-under-water và return per unit drawdown là trọng tâm chính. Objective nghiên cứu không phải raw return đơn thuần mà là cân bằng giữa lợi nhuận, drawdown và turnover.

## Stress-Test Analysis

Stress years used: `2018, 2020, 2022, 2023, 2026`.

| year | strategy                       | total_return | calmar_ratio | sortino_ratio | max_drawdown | cvar_95_daily | exposure_ratio |
| ---- | ------------------------------ | ------------ | ------------ | ------------- | ------------ | ------------- | -------------- |
| 2018 | Buy & Hold                     | 49.21%       | 2.101        | 1.626         | -23.65%      | -5.31%        | 100.00%        |
| 2020 | Buy & Hold                     | -5.91%       | -0.156       | -0.033        | -37.99%      | -5.17%        | 100.00%        |
| 2022 | Buy & Hold                     | -43.43%      | -0.887       | -2.261        | -49.38%      | -5.08%        | 100.00%        |
| 2023 | Buy & Hold                     | -17.10%      | -0.371       | -0.570        | -46.56%      | -4.93%        | 100.00%        |
| 2026 | Buy & Hold                     | 30.13%       | 2.185        | 1.828         | -32.07%      | -6.97%        | 100.00%        |
| 2018 | MACD only                      | 19.87%       | 0.723        | 0.919         | -27.72%      | -3.30%        | 48.40%         |
| 2020 | MACD only                      | 4.94%        | 0.427        | 0.328         | -11.56%      | -2.90%        | 47.62%         |
| 2022 | MACD only                      | -12.91%      | -0.448       | -0.482        | -29.15%      | -3.72%        | 49.40%         |
| 2023 | MACD only                      | -0.14%       | -0.006       | 0.144         | -23.57%      | -3.81%        | 60.64%         |
| 2026 | MACD only                      | 56.68%       | 9.150        | 2.587         | -16.09%      | -5.55%        | 45.60%         |
| 2018 | Ito-Bayes-GARCH only           | 53.08%       | 3.010        | 1.681         | -17.81%      | -4.52%        | 66.80%         |
| 2020 | Ito-Bayes-GARCH only           | -3.39%       | -0.604       | -0.324        | -5.62%       | -0.03%        | 5.16%          |
| 2022 | Ito-Bayes-GARCH only           | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |
| 2023 | Ito-Bayes-GARCH only           | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |
| 2026 | Ito-Bayes-GARCH only           | -2.51%       | -0.155       | 0.213         | -32.07%      | -6.97%        | 85.60%         |
| 2018 | MACD + Ito hard gate           | 23.18%       | 1.076        | 0.924         | -21.74%      | -3.13%        | 33.20%         |
| 2020 | MACD + Ito hard gate           | -3.55%       | -1.000       | -0.291        | -3.55%       | -0.01%        | 1.59%          |
| 2022 | MACD + Ito hard gate           | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |
| 2023 | MACD + Ito hard gate           | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |
| 2026 | MACD + Ito hard gate           | 31.09%       | 4.511        | 1.558         | -16.09%      | -5.55%        | 36.80%         |
| 2018 | MACD + volatility targeting    | 8.83%        | 0.799        | 1.003         | -11.13%      | -1.34%        | 20.53%         |
| 2020 | MACD + volatility targeting    | -3.65%       | -0.507       | -0.354        | -7.19%       | -1.30%        | 22.11%         |
| 2022 | MACD + volatility targeting    | -8.73%       | -0.577       | -1.060        | -15.30%      | -1.42%        | 21.07%         |
| 2023 | MACD + volatility targeting    | 2.86%        | 0.505        | 0.434         | -5.75%       | -1.26%        | 25.87%         |
| 2026 | MACD + volatility targeting    | 10.70%       | 6.348        | 2.263         | -3.58%       | -1.26%        | 10.50%         |
| 2018 | MACD + tail-risk gate          | 6.92%        | 1.700        | 0.475         | -4.10%       | -0.02%        | 4.40%          |
| 2020 | MACD + tail-risk gate          | -0.62%       | -0.186       | -0.060        | -3.31%       | -0.39%        | 8.33%          |
| 2022 | MACD + tail-risk gate          | 1.25%        | 0.981        | 0.294         | -1.29%       | -0.01%        | 2.41%          |
| 2023 | MACD + tail-risk gate          | 5.59%        | 23.590       | 5.233         | -0.24%       | -0.00%        | 4.42%          |
| 2026 | MACD + tail-risk gate          | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |
| 2018 | MACD + full defensive exposure | 3.99%        | 1.611        | 0.446         | -2.50%       | -0.01%        | 2.58%          |
| 2020 | MACD + full defensive exposure | -0.38%       | -0.191       | -0.080        | -2.00%       | -0.28%        | 5.56%          |
| 2022 | MACD + full defensive exposure | 0.79%        | 0.973        | 0.290         | -0.82%       | -0.00%        | 1.53%          |
| 2023 | MACD + full defensive exposure | 3.52%        | 23.007       | 5.160         | -0.15%       | -0.00%        | 2.84%          |
| 2026 | MACD + full defensive exposure | 0.00%        |              |               | 0.00%        | 0.00%         | 0.00%          |

## Regime Analysis

| regime   | observations | total_return | calmar_ratio | sortino_ratio | max_drawdown | exposure_ratio |
| -------- | ------------ | ------------ | ------------ | ------------- | ------------ | -------------- |
| panic    | 2,116.00     | 1.06%        | 0.128        | 0.486         | -0.98%       | 0.24%          |
| recovery | 10.00        | -0.33%       | -13.632      | -1.823        | -0.59%       | 24.61%         |
| sideways | 228.00       | 7.03%        | 1.273        | 1.295         | -6.12%       | 29.76%         |
| trend_up | 20.00        | 2.01%        | 39.094       | 3.342         | -0.73%       | 20.10%         |

## Drawdown and Tail-Risk Analysis

Hybrid model dùng volatility targeting để giảm exposure mềm khi forecast volatility cao, tail-risk gate để exit cứng khi predictive p05 thấp hơn loss floor, uncertainty penalty để giảm vốn khi predictive interval rộng bất thường, và equity/profit protection để bảo vệ đường vốn khi drawdown hoặc volatility xấu đi.

## Interpretation

Nếu hybrid giảm max drawdown và cải thiện Calmar/Sortino so với MACD only, Ito-Bayes-GARCH layer đang tạo giá trị như một risk governor. Nếu hybrid làm mất quá nhiều return mà không cải thiện risk-adjusted metrics, risk governor đang quá bảo thủ hoặc target volatility/loss floor cần chỉnh lại trên validation.

## Limitations

- GARCH posterior vẫn là asymptotic approximation quanh MLE, chưa phải MCMC đầy đủ.
- Profit protection là path-dependent risk rule, cần stress-test thêm với phí và slippage.
- Regime detection hiện chỉ để giải thích, chưa tối ưu hoặc điều khiển position.
- Dữ liệu chỉ một tài sản theo config, chưa kiểm định cross-asset rộng.

## Next Development Steps

1. Tối ưu target volatility, loss floor và uncertainty penalty trên validation, giữ test cuối độc lập.
2. Thử walk-forward refit theo quý hoặc tháng nếu laptop cho phép.
3. Kiểm định độ nhạy transaction cost.
4. Thử regime detection làm overlay nhẹ sau khi report-only diagnostic ổn định.

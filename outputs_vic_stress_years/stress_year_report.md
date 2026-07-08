# Báo Cáo Stress-Year Backtest

## Phương Pháp Chọn Năm

Các năm stress được chọn từ chuỗi benchmark return của tài sản bằng composite score:

- annualized volatility cao,
- downside volatility cao,
- max drawdown sâu,
- annual return yếu.

Mỗi năm được backtest độc lập với capital reset về `1.0` ở đầu năm. Các năm được chọn: `2022, 2023, 2026, 2020, 2018`.

## Xếp Hạng Năm Stress

| year | sessions | benchmark_return | annual_volatility | max_drawdown | worst_day | stress_score |
| ---- | -------- | ---------------- | ----------------- | ------------ | --------- | ------------ |
| 2022 | 249      | -43.43%          | 32.73%            | -49.38%      | -6.93%    | 13           |
| 2023 | 249      | -17.10%          | 33.35%            | -46.56%      | -7.00%    | 13           |
| 2026 | 125      | 30.13%           | 55.62%            | -32.07%      | -7.00%    | 16           |
| 2020 | 252      | -5.91%           | 32.40%            | -37.99%      | -6.98%    | 17           |
| 2018 | 250      | 49.21%           | 34.01%            | -23.65%      | -7.01%    | 22           |
| 2021 | 250      | -1.12%           | 31.77%            | -33.12%      | -7.00%    | 23           |
| 2025 | 249      | 736.70%          | 48.76%            | -17.46%      | -7.00%    | 25           |
| 2024 | 250      | -9.10%           | 21.84%            | -17.53%      | -5.33%    | 30           |
| 2016 | 251      | 25.04%           | 27.86%            | -18.74%      | -5.90%    | 31           |
| 2017 | 250      | 83.99%           | 25.01%            | -13.89%      | -4.28%    | 40           |

## Backtest 3 Chiến Lược Trong Các Năm Stress

| year | strategy        | total_points | total_return | annual_volatility | sharpe | max_drawdown | var_95_daily | cvar_95_daily | win_rate | exposure | trades |
| ---- | --------------- | ------------ | ------------ | ----------------- | ------ | ------------ | ------------ | ------------- | -------- | -------- | ------ |
| 2022 | Ito Bayes-GARCH | 0.00         | 0.00%        | 0.00%             |        | 0.00%        | 0.00%        | 0.00%         | 0.00%    | 0.00%    | 0      |
| 2022 | MACD(6,26,12)   | -5.36        | -12.91%      | 25.03%            | -0.435 | -29.15%      | -2.19%       | -3.72%        | 21.29%   | 49.40%   | 10     |
| 2022 | Buy & Hold      | -20.65       | -43.43%      | 32.73%            | -1.597 | -49.38%      | -3.18%       | -5.08%        | 42.97%   | 100.00%  | 1      |
| 2023 | Ito Bayes-GARCH | 0.00         | 0.00%        | 0.00%             |        | 0.00%        | 0.00%        | 0.00%         | 0.00%    | 0.00%    | 0      |
| 2023 | MACD(6,26,12)   | 0.87         | -0.14%       | 26.33%            | 0.125  | -23.57%      | -2.26%       | -3.81%        | 23.69%   | 60.64%   | 9      |
| 2023 | Buy & Hold      | -4.60        | -17.10%      | 33.35%            | -0.403 | -46.56%      | -2.78%       | -4.93%        | 40.16%   | 100.00%  | 1      |
| 2026 | Ito Bayes-GARCH | -11.67       | -7.92%       | 50.44%            | -0.079 | -33.12%      | -6.94%       | -6.97%        | 38.40%   | 79.20%   | 11     |
| 2026 | MACD(6,26,12)   | 83.99        | 56.68%       | 39.30%            | 2.501  | -16.09%      | -2.96%       | -5.55%        | 26.40%   | 45.60%   | 4      |
| 2026 | Buy & Hold      | 51.10        | 30.13%       | 55.62%            | 1.232  | -32.07%      | -6.94%       | -6.97%        | 50.40%   | 100.00%  | 1      |
| 2020 | Ito Bayes-GARCH | 0.53         | 1.07%        | 2.25%             | 0.485  | -1.33%       | 0.00%        | -0.01%        | 1.19%    | 1.98%    | 4      |
| 2020 | MACD(6,26,12)   | 1.34         | 4.94%        | 19.50%            | 0.344  | -11.56%      | -1.60%       | -2.90%        | 19.05%   | 47.62%   | 13     |
| 2020 | Buy & Hold      | -3.02        | -5.91%       | 32.40%            | -0.027 | -37.99%      | -3.12%       | -5.17%        | 42.06%   | 100.00%  | 1      |
| 2018 | Ito Bayes-GARCH | 10.36        | 37.70%       | 28.35%            | 1.280  | -21.02%      | -2.18%       | -4.52%        | 33.20%   | 61.60%   | 5      |
| 2018 | MACD(6,26,12)   | 4.99         | 19.87%       | 21.57%            | 0.954  | -27.72%      | -1.97%       | -3.30%        | 25.20%   | 48.40%   | 13     |
| 2018 | Buy & Hold      | 13.97        | 49.21%       | 34.01%            | 1.357  | -23.65%      | -3.18%       | -5.31%        | 50.40%   | 100.00%  | 1      |

## Đọc Nhanh Theo Từng Năm

- `2022`: return tốt nhất là `Ito Bayes-GARCH` (0.00%); drawdown thấp nhất là `Ito Bayes-GARCH` (0.00%); Sharpe tốt nhất là `MACD(6,26,12)` (-0.435).
- `2023`: return tốt nhất là `Ito Bayes-GARCH` (0.00%); drawdown thấp nhất là `Ito Bayes-GARCH` (0.00%); Sharpe tốt nhất là `MACD(6,26,12)` (0.125).
- `2026`: return tốt nhất là `MACD(6,26,12)` (56.68%); drawdown thấp nhất là `MACD(6,26,12)` (-16.09%); Sharpe tốt nhất là `MACD(6,26,12)` (2.501).
- `2020`: return tốt nhất là `MACD(6,26,12)` (4.94%); drawdown thấp nhất là `Ito Bayes-GARCH` (-1.33%); Sharpe tốt nhất là `Ito Bayes-GARCH` (0.485).
- `2018`: return tốt nhất là `Buy & Hold` (49.21%); drawdown thấp nhất là `Ito Bayes-GARCH` (-21.02%); Sharpe tốt nhất là `Buy & Hold` (1.357).

## Nhận Xét Tổng Quát

- Ito Bayes-GARCH là bộ lọc phòng thủ dựa trên volatility và drift. Mô hình giảm exposure rất mạnh trong năm giảm sâu, nhờ đó kiểm soát drawdown tốt, nhưng dễ bỏ lỡ pha hồi phục nhanh.
- MACD(6,26,12) phản ứng tốt hơn với trend và momentum. Vì vậy MACD thường vượt trội ở các năm có hồi phục rõ hoặc xu hướng tăng mạnh sau cú sốc.
- Buy & Hold thắng khi cả năm có xu hướng tăng rất mạnh, nhưng chịu toàn bộ drawdown và tail risk trong giai đoạn bán tháo.
- Kết quả stress-test gợi ý hướng phát triển hợp lý là dùng MACD để xác nhận xu hướng, còn Ito Bayes-GARCH làm lớp quản trị rủi ro, điều chỉnh exposure hoặc position sizing.

# Short-Term Reversal Alpha Research on Nasdaq 100

## 1. Project Overview

This project evaluates whether a 5-day short-term reversal signal can generate robust and tradable alpha on Nasdaq 100 constituents.

The project is designed as an end-to-end equity statistical arbitrage research pipeline. It starts from a simple factor hypothesis, evaluates its predictive power, builds long-short backtests, incorporates transaction costs and turnover control, and then performs out-of-sample validation, regime analysis, sector exposure diagnostics, and beta-neutral robustness tests.

The goal of this project is not to present a production-ready profitable trading strategy. Instead, it demonstrates a rigorous alpha research process and evaluates whether a statistically interesting signal remains robust after realistic trading frictions and risk controls.

---

## 2. Research Question

The main research question is:

> Can a 5-day short-term reversal signal generate robust net alpha after transaction costs, turnover control, out-of-sample testing, and risk neutralization?

The reversal hypothesis is based on the idea that stocks with strong short-term underperformance may experience short-term mean reversion, while stocks with strong short-term outperformance may subsequently underperform.

---

## 3. Data

The project uses daily price data for Nasdaq 100 constituents from 2015 to 2025.

Main data inputs include:

- Daily close prices
- Daily stock returns
- 5-day reversal factor
- 60-day trend factor
- Sector classifications
- Rolling stock beta estimates

The market return proxy is constructed as the equal-weighted daily return of the stock universe.

---

## 4. Factor Construction

The main factor is a 5-day short-term reversal signal:

```python
reversal_5d = -(close_prices / close_prices.shift(5) - 1)
```

A higher factor value means the stock has performed worse over the past 5 trading days and is therefore considered a stronger short-term reversal candidate.

Interpretation:

- High `reversal_5d`: short-term loser, potential long candidate
- Low `reversal_5d`: short-term winner, potential short candidate

---

## 5. Research Pipeline

The project follows the research workflow below:

1. Data loading and cleaning
2. Factor construction
3. Information Coefficient analysis
4. Quantile return analysis
5. Baseline long-short backtest
6. Transaction cost sensitivity analysis
7. Turnover reduction using buffer rules
8. In-sample, validation, and out-of-sample testing
9. Market regime analysis
10. Strict regime-aware backtesting
11. Multi-factor extensions
12. Sector exposure analysis
13. Sector-neutral backtesting
14. Beta exposure analysis
15. Beta-neutral backtesting
16. Final performance and risk diagnostics

---

## 6. Backtest Design

The core strategy is a long-short equity strategy.

At each rebalance date:

- Long stocks with the highest reversal scores
- Short stocks with the lowest reversal scores
- Long exposure is normalized to +1
- Short exposure is normalized to -1
- Net dollar exposure is approximately 0
- Gross exposure is approximately 2

The main implementation uses a 5-day rebalance frequency and a buffer rule to reduce turnover.

Transaction costs are modeled in basis points as a one-way transaction cost applied to portfolio turnover.

---

## 7. Buffer Rule

A buffer rule is introduced to reduce unnecessary trading.

For the Buffer 60% version:

- New long positions enter from the top 20% of reversal scores
- Existing long positions are kept until they fall below the top 60%
- New short positions enter from the bottom 20%
- Existing short positions are kept until they rise above the bottom 60%

This reduces turnover by avoiding full portfolio replacement at every rebalance date.

---

## 8. Main Results

The main strategy comparison is shown below.

| Strategy | Final Cumulative Return | Total Return (%) | Annualized Return (%) | Annualized Volatility (%) | Sharpe Ratio | Max Drawdown (%) |
|---|---:|---:|---:|---:|---:|---:|
| Reversal-only Buffer 60% | 1.101 | 10.062 | 0.878 | 15.433 | 0.057 | -35.156 |
| Strict Regime-aware Buffer 60% | 1.073 | 7.347 | 0.648 | 13.980 | 0.046 | -26.504 |
| Combined Reversal + Trend Buffer 60% | 0.693 | -30.747 | -3.293 | 13.454 | -0.245 | -50.730 |
| Trend-filtered Reversal Buffer 60% | 0.434 | -56.626 | -7.330 | 17.752 | -0.413 | -65.027 |
| Sector-neutral Reversal Buffer 60% | 0.728 | -27.151 | -2.846 | 12.128 | -0.235 | -41.064 |
| Beta-neutral Reversal Buffer 60% | 0.854 | -14.554 | -1.423 | 13.525 | -0.105 | -42.613 |

The original Reversal-only Buffer 60% strategy produces weak positive full-sample performance. However, all stricter extensions and risk-neutral versions fail to produce robust improvements.

---

## 9. Out-of-Sample Validation

The sample is divided into three periods:

- In-sample: 2015-2019
- Validation: 2020-2022
- Out-of-sample: 2023-2025

For the Reversal-only Buffer 60% strategy:

| Period | Total Return (%) | Annualized Return (%) | Sharpe Ratio | Max Drawdown (%) |
|---|---:|---:|---:|---:|
| In-sample 2015-2019 | 25.301 | 4.622 | 0.424 | -24.154 |
| Validation 2020-2022 | 5.239 | 1.717 | 0.087 | -31.924 |
| Out-of-sample 2023-2025 | -16.535 | -5.885 | -0.350 | -24.648 |

The strategy performs best in the in-sample period, weakens in the validation period, and becomes negative in the out-of-sample period. This suggests that the signal is not robust through time.

---

## 10. Regime Analysis

A market regime analysis is performed using the equal-weighted market cumulative return and a 60-day moving average.

The reversal strategy performs better in neutral or weak-trend environments and performs worse in strong uptrend environments.

A strict regime-aware backtest is also implemented. Although it reduces maximum drawdown, it does not materially improve the final net performance.

This indicates that regime information has explanatory value, but a simple regime filter does not create a robust tradable improvement.

---

## 11. Multi-Factor Extensions

Two trend-based multi-factor extensions are tested:

1. Additive Reversal + Trend factor
2. Trend-filtered Reversal strategy

Both extensions perform worse than the original reversal-only strategy.

This suggests that simply adding a medium-term trend signal does not stabilize the short-term reversal alpha in this universe and backtest setting.

---

## 12. Sector Exposure Analysis

The original Reversal-only Buffer 60% strategy does not have a large persistent average sector bias, but it has substantial time-varying sector exposures, especially in Technology.

Sector-neutralization is then applied by ranking stocks within each sector separately.

The sector-neutral strategy successfully reduces average absolute net sector exposure to approximately zero. However, its performance deteriorates significantly.

This suggests that part of the original strategy performance may come from dynamic sector exposures rather than a clean stock-specific reversal alpha.

---

## 13. Beta Exposure Analysis

Although the original strategy is dollar-neutral by construction, it is not consistently beta-neutral through time.

Original portfolio beta statistics:

| Metric | Value |
|---|---:|
| Mean portfolio beta | -0.0269 |
| Std portfolio beta | 0.3239 |
| Min portfolio beta | -1.0506 |
| Max portfolio beta | 1.0072 |

After beta-neutralization:

| Metric | Value |
|---|---:|
| Mean portfolio beta | 0.0007 |
| Std portfolio beta | 0.0143 |
| Min portfolio beta | -0.1048 |
| Max portfolio beta | 0.2243 |

Beta-neutralization successfully reduces portfolio beta exposure. However, the beta-neutral strategy performs worse than the original strategy.

This suggests that part of the original performance may come from time-varying market beta exposure.

---

## 14. Key Findings

The main findings are:

1. The 5-day reversal factor shows weak gross predictive power.
2. Transaction costs significantly reduce strategy performance.
3. Buffer rules reduce turnover and improve net performance relative to the naive strategy.
4. Full-sample weak positive performance does not survive robust out-of-sample testing.
5. The strategy performs poorly in the 2023-2025 out-of-sample period.
6. Regime analysis suggests that reversal works better in neutral or weak-trend markets.
7. Trend-based multi-factor extensions do not improve performance.
8. Sector-neutralization removes sector exposure but weakens performance.
9. Beta-neutralization removes market beta exposure but also weakens performance.
10. The original strategy's weak positive performance is likely partly driven by dynamic sector and beta exposures.

---

## 15. Final Conclusion

The 5-day short-term reversal signal is statistically interesting but not strong enough as a standalone tradable alpha after realistic transaction costs, out-of-sample testing, and risk neutralization.

The project shows that a signal may appear promising under simple backtests, but its performance can weaken substantially once turnover, transaction costs, sample stability, sector exposure, and beta exposure are properly considered.

This project therefore demonstrates a complete alpha research workflow rather than a production-ready trading strategy.

---

## 16. Limitations

Several limitations remain:

1. The universe is limited to Nasdaq 100 constituents.
2. Survivorship bias may exist if current constituents are used historically.
3. The transaction cost model is simplified.
4. Borrowing costs and short-sale constraints are not modeled.
5. Sector classifications are based on available current sector labels.
6. The strategy uses daily close-to-close returns and does not model intraday execution.
7. Capacity and market impact are not analyzed.
8. The market proxy is an equal-weighted universe return rather than an external benchmark.

---

## 17. Possible Next Steps

Potential future extensions include:

1. Use historical Nasdaq 100 constituents to reduce survivorship bias.
2. Add borrow costs and short availability constraints.
3. Test the signal on a broader US equity universe.
4. Use residual returns after removing sector and market effects.
5. Build industry-neutral residual reversal signals.
6. Incorporate a more realistic transaction cost and market impact model.
7. Evaluate capacity and liquidity constraints.
8. Compare reversal alpha across different market regimes and universes.
9. Explore alternative holding periods and rebalance frequencies with proper out-of-sample validation.
10. Use a formal risk model for factor exposure control.

---

## 18. Skills Demonstrated

This project demonstrates:

- Python-based financial data analysis
- Pandas and NumPy time-series processing
- Equity factor construction
- Information Coefficient analysis
- Quantile portfolio analysis
- Long-short backtesting
- Transaction cost modeling
- Turnover control
- Out-of-sample validation
- Market regime analysis
- Sector exposure diagnostics
- Rolling beta estimation
- Beta-neutral and sector-neutral robustness testing
- Research interpretation and alpha decay analysis

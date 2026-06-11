# Intraday Pairs Trading with skfolio

Extends skfolio with a data-driven pairs trading workflow on 52 large-cap US
equities — 10 years of minute-level data (Polygon) resampled to 30-minute bars.

## Workflow

1. Data pipeline — 52 minute-bar parquet files, aligned to NY time,
   market hours only, resampled to 30-min bars.

2. Pair selection, two approaches compared:
   - Cointegration (Engle-Granger on daily log prices): statistically valid
     pairs, but estimated half-lives of 100+ trading days — too slow to harvest
     with an intraday signal. OOS annualized Sharpe ~0.2.
   - Correlation (30-min returns, hedge ratios estimated at the same frequency
     as the signal): OOS annualized Sharpe ~0.75.

3. Signal — beta-hedged return residual, z-scored on a 78-bar rolling window.
   Stateful position logic: enter at |z| > 2, exit on reversion to 0,
   stop loss at |z| > 3.

4. Portfolio construction — skfolio RiskBudgeting (variance risk parity)
   across 20 pairs; also compared HierarchicalRiskParity and Ledoit-Wolf
   covariance shrinkage (OOS results materially unchanged — signal quality
   dominates optimizer choice).

5. Validation — chronological 67/33 train/test split; pairs, alphas, and
   betas frozen on train. QuantStats tearsheet with correct intraday
   annualization (252 x 13 periods per year).

## Key finding

Match the statistical method to the trading frequency. Cointegration captures
long-run equilibria an intraday signal cannot harvest; same-frequency
correlation-based hedge ratios improved OOS Sharpe ~3x.

## Files

- Pairs_trading.ipynb — annotated end-to-end workflow
- pairs_trading_report_cor.html — OOS QuantStats tearsheet

## Requirements

skfolio, quantstats, statsmodels, scikit-learn, pandas, numpy


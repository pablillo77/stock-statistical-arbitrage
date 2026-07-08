[README.md](https://github.com/user-attachments/files/29819202/README.md)
# Stock Anomaly Detection with PCA (Statistical Arbitrage)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/pablillo77/stock-statistical-arbitrage/blob/main/Stock_anomalies_detection_with_PCA_teaching.ipynb)

A quantitative research tool that flags **mean-reversion candidates** across an equity universe. Most of a stock's daily move reflects the market, its sector, and rates, not the stock itself. This project strips out those common (systematic) factors with PCA and studies what's left: the **residual**, the stock-specific part of the return. Residuals that drift far from zero *and* historically revert become trade candidates.

Built as a decision-support and teaching tool. The notebook is written to be read top to bottom, with the reasoning behind each step spelled out.

---

## The pipeline

1. **Universe & returns.** 38 tickers across 5 sectors; daily **log returns** (additive over time, so accumulated residuals stay clean).
2. **Windowing.** Analysis runs on a recent window; tickers with over 2% missing days are dropped so a ragged panel can't poison the covariance matrix.
3. **PCA factor extraction.** Standardized returns, then correlation matrix, then eigendecomposition. PC1 is essentially "the market"; higher PCs split sectors. **K = 8 factors** retained.
4. **Factor regression to residuals.** Each stock is regressed (OLS) on the factors; the residual is what the factors *cannot* explain.
5. **s-score & half-life.** Daily residuals are accumulated to expose slow drifts; the **s-score** measures how unusual today's accumulated residual is, and an **AR(1) / Ornstein-Uhlenbeck fit** estimates the mean-reversion **half-life**.
6. **Signal logic.** A candidate must clear *both* filters: an s-score large enough to matter **and** a half-life inside a tradeable band. Negative s beyond the threshold means LONG, positive means SHORT.
7. **Market-regime check.** Mean pairwise correlation across the universe flags when everything is moving together (dispersion low, so reversion signals are less reliable).
8. **Diagnostics.** Residual-path plots for top candidates, a scree plot to sanity-check K, and a 3D factor-space scatter (loadings on the first three PCs) that should separate sectors visually.
9. **News check.** The factor model is blind to news. Any name firing a signal or moving over 3% today is cross-checked against recent headlines, so **event-driven repricings are distinguished from genuine statistical dislocations** (a repricing has no reason to revert).
10. **Optional logging.** Append the daily watchlist to a CSV to review, weeks later, how signals actually played out.

## Tech stack

Python, NumPy, pandas, scikit-learn, SciPy, Matplotlib, yfinance.

## Run it

Click the **Open in Colab** badge above. It installs dependencies and runs end to end, no local setup.

## Disclaimer

Research and educational tool for signal exploration. **Not investment advice** and not a production trading system.

---

*Author: Pablo Giménez. MSc Electronics Engineering, MSc Biomedical Engineering.*

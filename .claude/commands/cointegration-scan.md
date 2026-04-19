---
description: Scan a basket of FX pairs for cointegration candidates suitable for statistical-arbitrage / mean-reversion setups.
argument-hint: [BASKET (default: G10-crosses)]
---

# Cointegration Scan

## Procedure

1. **Basket selection** — default G10 crosses (EURUSD, GBPUSD, USDJPY, USDCHF, AUDUSD, NZDUSD, USDCAD, EURGBP, EURJPY, EURCHF, AUDJPY, GBPJPY). Override via arg.
2. **Data** — `fawazahmed-exchange-api`, common window (default 3y daily).
3. **Stationarity** — ADF via `statsmodels` on each log-price.
4. **Pairwise Engle-Granger** — for each unordered pair, run the cointegration test. Keep those with p < 0.05.
5. **Johansen** (optional) — for any triple of survivors, run Johansen to check for higher-dim cointegration.
6. **Half-life** — for each cointegrated pair, fit OU / AR(1) on the spread, report half-life of mean reversion.
7. **Current z-score** — spread z-score today. Flag |z| > 2 as actionable.
8. **Report** — Typst PDF `reports/cointegration-scan-<date>.pdf` with ranked table and spread charts for top candidates.

## Output

Table: pair | p-value | half-life (days) | current z-score | action flag.

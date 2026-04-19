---
description: Run a full FX pair analysis — fetch history, compute indicators + quant metrics, detect support/resistance, pull macro + geopol context, and generate a Typst PDF report.
argument-hint: <PAIR> [timeframe]
---

# Analyze FX Pair

End-to-end pipeline for a single FX pair. Produces a dated PDF in `reports/`.

## Inputs

- **Pair** — e.g. `EURUSD`, `USDJPY`, `GBPUSD`. Required.
- **Timeframe** — `1y`, `5y`, `10y`, `max`. Default `5y`.

## Procedure

Follow the Planning-with-Files pattern (`toolkit.json` id `clarity-planning-pattern`). Seed `context/from-agent/task_plan.md`, update `progress.md` after each phase, write findings to `findings.md`.

### Phase 1 — Data ingestion

Load `toolkit.json`. Use tool `fawazahmed-exchange-api` for daily close series. Fall back to `yfinance` if OHLC is needed. Cache raw data to `data/<pair>/<timeframe>.parquet`.

Pull macro context via `fred` — at minimum the rate-differential series for the pair's two currencies (policy rate, 2Y yield, 10Y yield) and DXY.

### Phase 2 — Technical analysis

Use `pandas-ta` for: 20/50/200 SMA + EMA, RSI(14), MACD(12,26,9), Bollinger(20,2), ATR(14), pivot points. Write a `findings.md` section per indicator.

Run `detect-support-resistance` skill (or compute inline): swing highs/lows, Fibonacci retracements from the most recent trend leg, horizontal clusters.

### Phase 3 — Quant metrics

Compute log-returns. Via `quantstats`: Sharpe, Sortino, Calmar, max drawdown, VaR(95/99), CVaR, monthly heatmap, rolling 30d vol. Export tearsheet HTML.

Run stationarity + cointegration via `statsmodels`: ADF on the log-price, note trend/mean-reverting character.

Model vol via `arch` — fit GARCH(1,1), report persistence and a 20-day vol forecast.

### Phase 4 — Geopolitical & macro context

Use `tavily` for recent news (last 90 days) around the two currencies' central banks and any relevant geopolitical events. Use `perplexity` for a synthesised geopol brief keyed to the analysis window.

Build a signal log (pattern `rkiding-signal-tracker`) — for each signal identified, note strengthen / weaken / falsify conditions.

### Phase 5 — Report

Run the `generate-fx-report` skill. Typst PDF to `reports/<pair>-<YYYY-MM-DD>.pdf` containing:

1. Executive summary (3 bullets: bias, risk, catalyst)
2. Price action & key levels (chart + S/R table)
3. Technical indicators (chart grid + reading)
4. Quant metrics table (excerpted from quantstats tearsheet)
5. Vol forecast (GARCH)
6. Macro & rate differential
7. Geopolitical context
8. Signal log with falsifiers
9. Data provenance (which tools + endpoints + timestamp)

## Output discipline

- Never dump raw time series into the response. Use programmatic tool calling (`langalpha-ptc`) — write Python, compute, return summary only.
- All charts go to `charts/<pair>/` as PNG + SVG.
- Cite every numeric claim to its source series and timestamp.
- Final message: one-paragraph bias statement, key levels, top risk, path to the PDF.

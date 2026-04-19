---
description: Side-by-side comparison of 2-6 FX pairs — correlations, cointegration, relative performance, vol, regime.
argument-hint: <PAIR1> <PAIR2> [PAIR3...]
---

# Compare Pairs

## Procedure

1. **Align data** — pull daily close for all pairs (`fawazahmed-exchange-api`), align on common dates, compute log-returns.
2. **Correlation matrix** — Pearson + Spearman, full window + rolling 90d. Heatmap to `charts/compare/`.
3. **Cointegration** — pairwise Engle-Granger via `statsmodels`. Flag any cointegrated pairs as candidates for mean-reversion trades.
4. **Relative performance** — rebase all pairs to 100 at window start. Chart.
5. **Vol comparison** — `arch` GARCH per pair, compare persistence + unconditional vol.
6. **Quant metrics table** — `quantstats` row per pair: Sharpe, Sortino, max DD, CAGR.
7. **Macro framing** — for each pair, note current rate differential (`fred`).
8. **Report** — Typst PDF `reports/compare-<slug>-<date>.pdf`.

## Output

Ranked table of the pairs on a composite score (Sharpe + trend strength + vol regime), plus any cointegrated-pair trade setups.

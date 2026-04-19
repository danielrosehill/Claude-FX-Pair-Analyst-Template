---
description: Characterise the current volatility regime of an FX pair — realised vs implied, GARCH state, regime transitions.
argument-hint: <PAIR>
---

# Vol Regime

## Procedure

1. **Data** — daily closes (`fawazahmed-exchange-api`) for the last 5 years.
2. **Realised vol** — 30d, 90d, 1y rolling annualised vol.
3. **GARCH** — fit `arch` GARCH(1,1) and EGARCH(1,1,1). Report alpha, beta, persistence, unconditional vol, 20-day forecast.
4. **Regime detection** — rolling z-score of realised vol vs 2y baseline. Mark high/low/normal regimes on the chart.
5. **Implied vs realised** (optional) — if user supplies current ATM implied vol, compare and flag rich/cheap.
6. **Report** — short Typst PDF `reports/vol-<pair>-<date>.pdf`.

## Output

Current regime label + GARCH forecast + one-paragraph interpretation.

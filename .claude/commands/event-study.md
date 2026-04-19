---
description: Study an FX pair's reaction to a dated event — pre/post windows, abnormal returns, vol shift, and geopolitical framing.
argument-hint: <PAIR> <EVENT-DATE-YYYY-MM-DD> <EVENT-LABEL>
---

# Event Study

Quantify how a pair responded to a specific event (central bank decision, geopolitical incident, macro print).

## Procedure

1. **Window** — default [-30d, +30d] around event date. Override in `context/from-human/event.md` if needed.
2. **Data** — `fawazahmed-exchange-api` (or `oanda-v20` for intraday if the event was intraday-sensitive).
3. **Pre/post stats** — mean return, vol, skew, max adverse excursion in each window. Use `quantstats` + `statsmodels`.
4. **Abnormal return** — compute return vs a baseline expectation (rolling-30d mean or GARCH forecast via `arch`).
5. **Vol regime shift** — fit `arch` GARCH on pre-window, forecast post-window vol, compare to realised.
6. **Geopol framing** — `perplexity` brief on what the event was and the consensus narrative. `tavily` for contemporaneous headlines.
7. **Chart** — price path with event line, volume/vol overlay, annotated pre/post windows.
8. **Report** — short Typst PDF `reports/event-<pair>-<date>-<slug>.pdf`.

## Output

Concluding paragraph: magnitude of the move, whether it persisted, whether vol regime shifted, and the geopol interpretation.

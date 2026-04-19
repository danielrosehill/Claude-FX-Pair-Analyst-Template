---
description: Build a durable reference profile for an FX pair — historical drivers, structural levers, liquidity pockets, vol character. Saved to research/fxpairs/ as grounding for all subsequent analyses and hypothesis tests.
argument-hint: <PAIR>
---

# FX Pair Profiler

Produce a long-lived reference document for a pair. This is **grounding material**, not a point-in-time view. Subsequent runs of `/analyze-pair`, `/event-study`, and hypothesis tests (`/test-hypothesis`) read this file to seed their context.

Output path: `research/fxpairs/<PAIR>.md` (e.g. `research/fxpairs/EURUSD.md`).

If the file exists, **refresh** it — don't overwrite blindly. Preserve human edits, update timestamped sections, append a revision log.

## Scope

This command is explicitly about **structural** character, not tactical signals. Think: "what moves this pair on a decade-scale view?"

## Procedure

Follow the Planning-with-Files pattern (`clarity-planning-pattern` in `toolkit.json`). Use `langalpha-ptc` for quant computations — write Python, compute, return summary.

### Phase 1 — Identity & structural context

- Two currencies, ISO codes, issuer central banks, rate-setting cadence.
- Cross type: major / minor / exotic / commodity-linked / EM.
- Reserve-currency status of each leg. SDR weights.
- Peg / managed-float / free-float classification (flag IMF exchange-rate arrangement).
- Typical daily turnover from the most recent **BIS Triennial Survey** — cite the year.

### Phase 2 — Historical drivers (grounded research)

Research via `tavily` and `perplexity` with **explicit citation requirements** — every claim must have a source. Cover:

- Major historical regimes (pegs broken, devaluations, currency crises, policy-rate cycles).
- Known **structural drivers** (what has actually moved this pair historically):
  - Rate differentials (and which tenor matters most — 2Y vs 10Y).
  - Commodity linkages (AUD/oil+iron, CAD/oil, NOK/oil, CLP/copper, etc.).
  - Balance-of-payments / current-account dynamics.
  - Safe-haven flows (JPY, CHF, USD, gold-FX correlation).
  - Carry character — is this a funding currency or a target?
- **Presumed / analyst-consensus levers** (label clearly as consensus, not fact):
  - What mainstream research desks cite as the dominant driver today.
  - Known narrative shifts (e.g. "JPY lost pure safe-haven character post-2022").

### Phase 3 — Liquidity pockets

Quantify where liquidity concentrates:

- **Sessions** — compute average realised range by hour-of-day across Asia / London / NY sessions. Use intraday data if available (`oanda-v20`), else infer from daily open-close vs high-low decomposition.
- **Days of week** — Monday-Friday volume proxy (realised range).
- **Regular liquidity events** — London fix (16:00 GMT), NY close, month-end rebalancing, quarterly triple-witching.
- **Known thin windows** — holidays in either jurisdiction, Asian holiday clusters, post-NY-close gap zones for JPY/AUD/NZD.

Mark each liquidity pocket with a confidence (**measured** vs **presumed**).

### Phase 4 — Volatility character

- Long-run unconditional vol (5y, 10y, 20y if data permits). `arch` GARCH on daily log-returns.
- Vol clustering — GARCH alpha + beta, persistence.
- Vol-of-vol — rolling 30d vol series std dev.
- **Vol regimes observed historically** — label each (crisis, QE, hiking cycle, sideways) with approximate date ranges.
- Seasonality in vol (month-of-year, quarter).
- Tail behaviour — skew, kurtosis, largest 5 single-day moves in the last 10y with causes.

### Phase 5 — Correlations (structural, not rolling)

- 10y correlation with: DXY, SPX, gold, Brent, UST 2Y yield, UST 10Y yield, the other major crosses.
- Whether those correlations are stable or regime-dependent (compute in 2-3y sub-windows).

### Phase 6 — Research-backed hypothesis seeds

Seed 5-10 testable hypotheses the pair invites — these become the grounding for `/test-hypothesis` later. Examples:

- "EURUSD is primarily driven by 2Y rate differential" — testable via rolling regression.
- "AUDUSD leads iron ore by N days" — testable via lagged correlation.
- "USDJPY safe-haven character broke in 2022" — testable via correlation change-point.

Each hypothesis gets: claim, rationale, proposed test, data needed.

### Phase 7 — Provenance & revision log

Footer of the profile document:

- Generated: `YYYY-MM-DD HH:MM TZ`
- Data windows used, with start/end dates
- Tools used (from `toolkit.json`, by id)
- Sources cited (URLs, author, date accessed)
- **Revision log** — date + summary of each refresh

## Output structure (template)

The agent writes the profile in this order:

```markdown
# <PAIR> Pair Profile

> Durable reference document. Grounding for /analyze-pair, /event-study, /test-hypothesis.
> Last refreshed: YYYY-MM-DD. See revision log at bottom.

## 1. Identity
## 2. Historical drivers (grounded)
## 3. Presumed / consensus levers
## 4. Liquidity pockets
## 5. Volatility character
## 6. Structural correlations
## 7. Hypothesis seeds
## 8. Provenance
## 9. Revision log
```

## Final message

One paragraph: what kind of pair this is, the two or three dominant structural levers, and the most interesting hypothesis surfaced. Path to the saved file.

## Notes

- **Never fabricate citations.** If a fact can't be sourced, omit it or mark it `[unsourced — analyst inference]`.
- **Separate measured from presumed.** Liquidity, vol, correlations should be computed. Drivers and levers are a mix — label each.
- **Hypothesis seeds are the payoff.** Subsequent `/test-hypothesis <PAIR> <hypothesis>` runs will pick these up, so write them as genuine empirical questions with a proposed test.
- This file is cheap grounding — it can be regenerated. But it's also the entry point a human reads first, so write it for a human analyst, not just for the agent.

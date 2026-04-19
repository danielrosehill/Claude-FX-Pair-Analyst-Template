# FX Pair Profiles

Durable reference documents — one file per pair, e.g. `EURUSD.md`, `USDJPY.md`.

Built by `/profile-pair <PAIR>`. Refreshed (not overwritten) on subsequent runs.

## Why these exist

Every tactical analysis (`/analyze-pair`, `/event-study`, `/compare-pairs`) benefits from structural grounding: what actually drives this pair, where its liquidity lives, what its vol regime looks like on a decade-scale view. Rebuilding that context each run is wasteful and error-prone.

These profiles are the grounding layer. They persist across conversations and sessions.

## What goes in a profile

1. **Identity** — cross type, currency regime, BIS turnover rank
2. **Historical drivers** — grounded, cited
3. **Presumed levers** — consensus view, clearly labelled
4. **Liquidity pockets** — by session, day, known events
5. **Volatility character** — unconditional vol, clustering, regimes, tails
6. **Structural correlations** — DXY, equities, commodities, yields
7. **Hypothesis seeds** — testable questions that feed `/test-hypothesis`
8. **Provenance** — data windows, tools, citations
9. **Revision log**

## How other commands use these

- `/analyze-pair` — reads the profile for structural context before technical/quant analysis
- `/event-study` — uses the liquidity-pocket section to interpret windowing
- `/test-hypothesis` — operates directly on the hypothesis seeds; appends verdicts back into the profile

## Editing by hand

Human edits are welcome. Mark them with an HTML comment `<!-- human: note -->` so the agent preserves them on refresh.

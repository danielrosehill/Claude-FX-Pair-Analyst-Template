---
description: Test a specific hypothesis about an FX pair — e.g. "is EURUSD driven by 2Y rate differential?" or "did geopol event X move pair Y?". Grounded in the saved pair profile.
argument-hint: <PAIR> <HYPOTHESIS (quoted)>
---

# Test Hypothesis

Empirically test a claim about an FX pair. Reads the saved profile at `research/fxpairs/<PAIR>.md` for grounding.

## Procedure

1. **Load grounding** — read `research/fxpairs/<PAIR>.md`. If missing, run `/profile-pair <PAIR>` first and tell the user.
2. **Classify the hypothesis** — one of:
   - **Driver** — "X drives Y" → rolling regression / Granger causality
   - **Correlation change** — "relationship broke at date T" → change-point detection on rolling correlation
   - **Event impact** — "event E moved pair P" → event study (delegate to `/event-study`)
   - **Lead/lag** — "A leads B by N days" → lagged cross-correlation
   - **Regime** — "pair behaves differently in regime R" → conditional stats
3. **Pick the test** — state it explicitly before running. Include: null hypothesis, test statistic, significance threshold.
4. **Pull data** — spot (`fawazahmed-exchange-api` / `yfinance`), macro (`fred`), other series as needed.
5. **Run the test** — `statsmodels` for regressions / Granger / cointegration; `arch` for vol-related; `quantstats` for return-based claims. Use `langalpha-ptc` — write Python, return summary only.
6. **Robustness** — rerun on at least one sub-window and one alternate specification. Note if the result is fragile.
7. **Verdict** — one of: **supported** / **weakly supported** / **not supported** / **inconclusive**. With effect size and confidence.
8. **Write findings** — append to `research/fxpairs/<PAIR>.md` under `## Hypothesis tests` with date, claim, test, verdict, citation-ready summary.
9. **Report** — short PDF via `generate-fx-report` if the user asked, else a conversation summary.

## Output

Verdict first, then test, then effect size, then robustness. Path to the updated profile.

## Notes

- **No p-hacking.** Pick the test before looking at the result. Don't retry until you get significance.
- **Flag fragile results.** If significance depends on window choice, say so.
- **Cite the grounding.** Reference which section of the profile motivated the test.

## Causal claims (EconML use)

If the hypothesis is genuinely causal (not correlational) and you invoke `econml` from the toolkit, you **must**:

1. Name the confounder set explicitly before fitting. "Control variables: W = [DXY, VIX-proxy, rate_diff_2Y]."
2. Check overlap/positivity — treatment should have non-trivial variation across the confounder support. If not, abort.
3. Run at least one refutation test (placebo treatment, random common cause, subset robustness). EconML's DoWhy integration or manual re-fit on a permuted treatment.
4. Report the CATE with confidence interval width — a point estimate without CI is not a result.
5. State the assumption that makes the claim causal (unconfoundedness / positivity / no-interference) and whether you believe it holds in this FX context. If you don't believe it, say so and downgrade the claim to "partial effect conditional on observables".

Without these steps, downgrade the verdict language from "causes" to "is associated with after controlling for". Do not launder an associative finding as causal.

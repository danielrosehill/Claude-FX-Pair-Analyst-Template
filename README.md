> **Claude-native components live under `.claude/` — never at the repo root.** Slash commands go in `.claude/commands/`, skills in `.claude/skills/<name>/SKILL.md`, subagents in `.claude/agents/`. A root-level `./commands/`, `./skills/`, `./agents/`, `./slash-commands/`, or `./subagents/` is a bug — Claude Code will not discover them. Never add `.claude/` to `.gitignore`; always commit it (use `.gitkeep` if empty).

> This repository is a template provided as part of the [New Repo From Template Plugin](https://github.com/danielrosehill/New-Repo-From-Template-Plugin) for Claude Code.

# Claude FX Pair Analyst Template

A Claude Code workspace for analysing foreign exchange pairs — historical data retrieval, quant/econometric analysis, volatility modelling, support/resistance identification, grounded geopolitical framing, and PDF report generation.

## What this template gives you

- **A canonical tool manifest** (`toolkit.json`) — curated FX-relevant Python libraries, APIs, and MCPs. Skills and commands reference tools by id.
- **A grounding layer** (`research/fxpairs/`) — durable per-pair profiles (drivers, liquidity pockets, vol character, hypothesis seeds). Built once, refreshed over time, read by every subsequent analysis.
- **Workflow commands** that chain the toolkit:
  - `/profile-pair <PAIR>` — durable reference profile
  - `/analyze-pair <PAIR> [timeframe]` — full analysis + PDF
  - `/event-study <PAIR> <DATE> <LABEL>` — dated event reaction
  - `/compare-pairs <PAIR1> <PAIR2> …` — multi-pair comparison
  - `/cointegration-scan [BASKET]` — stat-arb candidate scan
  - `/vol-regime <PAIR>` — volatility regime characterisation
  - `/test-hypothesis <PAIR> "<claim>"` — empirical hypothesis testing
- **A shared Typst reporter** (`.claude/skills/generate-fx-report/`) — consistent PDF output across every command.

## Who it's for

Analysts, traders, and researchers who want a Claude-driven FX desk — opinionated about epistemics (measured vs presumed, falsifiable signals, cited claims) and pragmatic about tools (install-on-demand, lean dependencies, no crypto-only tooling).

## Not for

- Retail auto-trading signal generation
- Crypto-only analysis (see `goat-sdk` or `ccxt`-focused templates)
- High-frequency execution (see `nautilus_trader`)

## Quick start

1. Clone or scaffold from this template.
2. Open in Claude Code from the repo root.
3. For a new pair: `/profile-pair EURUSD` (builds grounding).
4. Then: `/analyze-pair EURUSD 5y` (produces PDF in `reports/`).

## Requirements

- Python 3.10+
- Typst CLI (`cargo install --locked typst-cli`) for PDF rendering
- Optional API keys for enhanced workflows:
  - `FRED_API_KEY` — macro series (recommended)
  - `ALPHAVANTAGE_API_KEY` — intraday FX
  - `OANDA_API_TOKEN` + `OANDA_ACCOUNT_ID` — broker-grade data
  - `NIXTLA_API_KEY` — TimeGPT forecasting
  - `PERPLEXITY_API_KEY` — geopolitical synthesis

The toolkit installs tools on demand — you won't pay for anything you don't use.

## How it's organised

```
toolkit.json                   # canonical tool manifest (libs, APIs, MCPs, patterns)
CLAUDE.md                      # the agent's role and working rules
research/fxpairs/              # durable pair profiles — grounding layer
data/                          # cached raw data
charts/                        # generated charts
reports/                       # final PDFs
context/from-human/            # your specific instructions
context/from-agent/            # the agent's working notes (task_plan / findings / progress)
.claude/commands/              # slash-command entry points
.claude/skills/                # shared skills (e.g. generate-fx-report)
```

## Epistemic discipline

This template enforces a few rules that distinguish it from generic "AI trading bot" scaffolds:

- **Every claim cites its source.** Historical and narrative claims need a URL or a dataset. Unsourced claims are marked `[unsourced — analyst inference]`.
- **Measured and presumed are separated.** Vol numbers are measured from data. "EURUSD is driven by rate differentials" is presumed unless tested — `/test-hypothesis` exists for that.
- **Signals are falsifiable.** Every logged signal carries strengthen / weaken / falsify conditions.
- **No p-hacking.** `/test-hypothesis` requires the test to be specified before looking at the result.

## License

MIT. Use it, fork it, improve it.

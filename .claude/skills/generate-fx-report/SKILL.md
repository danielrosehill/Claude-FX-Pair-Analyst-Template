---
name: generate-fx-report
description: Render a Typst PDF report for FX analyses — analyze-pair, event-study, compare-pairs, cointegration-scan, vol-regime. Accepts a structured payload (sections, tables, chart paths, provenance) and emits a dated PDF to reports/.
---

# Generate FX Report

Shared reporter. Every `/analyze-*` command calls this skill rather than writing Typst inline.

## Input contract

The caller passes a JSON payload:

```json
{
  "title": "EURUSD — Full Analysis",
  "subtitle": "5-year window",
  "pair": "EURUSD",
  "date": "YYYY-MM-DD",
  "summary_bullets": ["…", "…", "…"],
  "sections": [
    { "heading": "Price action", "body_md": "...", "charts": ["charts/eurusd/price.png"] },
    { "heading": "Technical indicators", "body_md": "...", "charts": ["..."] },
    { "heading": "Quant metrics", "table": [["Metric","Value"], ["Sharpe","0.43"]] }
  ],
  "signal_log": [ { "signal": "...", "strengthen": "...", "weaken": "...", "falsify": "..." } ],
  "provenance": { "tools": ["fawazahmed-exchange-api","arch","quantstats"], "data_window": "2020-04-20..2025-04-20", "sources": ["..."] }
}
```

## Procedure

1. Write `reports/.build/<pair>-<date>.typ` from the payload using the template below.
2. Run `typst compile reports/.build/<pair>-<date>.typ reports/<pair>-<date>.pdf`.
3. Return the PDF path.

## Typst template

Keep it minimal — one column, sans-serif, tight spacing. The caller is responsible for section content.

```typst
#set document(title: "<TITLE>")
#set page(margin: 2cm, numbering: "1")
#set text(font: "Inter", size: 10pt)
#set heading(numbering: "1.")

#align(center)[
  #text(18pt, weight: "bold")[<TITLE>]
  #v(0.3em)
  #text(11pt)[<SUBTITLE> · <DATE>]
]

#v(1em)
= Executive summary
#list(..<BULLETS>)

<SECTIONS>  // each: heading, md body, optional image(s), optional table

= Signal log
#table(columns: 4, [Signal],[Strengthen],[Weaken],[Falsify], ..<ROWS>)

= Provenance
Data window: <WINDOW> · Tools: <TOOLS>
#list(..<SOURCES>)
```

## Notes

- Require Typst on PATH. If missing, print the install command (`cargo install --locked typst-cli`) and fail loudly.
- Charts must already exist at the paths given — this skill doesn't render them.
- Body markdown is rendered via Typst's `#markdown` package if available, else converted to minimal Typst inline.
- Never embed raw time series — only charts + summary tables.

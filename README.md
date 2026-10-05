# Trading Prompts

Prompt specs for running a daily stock-research workflow with an AI agent. Each
prompt is a self-contained spec: it describes the task, the inputs, and the
output format, with no dependencies on who runs it or what account they hold.

## The prompts

| File | Purpose |
|---|---|
| `stock_summary_prompt.md` | Full stock report: per-ticker analysis plus a separate Picks section with trade ideas (strikes, expirations, thesis, conviction, risks) |
| `daily_macro_report.md` | Daily macro report: rates, inflation, growth, sector implications |
| `technical_analysis_prompt.md` | Technical read on each ticker: trend regime, momentum, support/resistance, ATR, volume confirmation |
| `stock_dd_prompt.md` | Deep-dive due diligence with an analyst-style FMV build (bear / base / bull) |

## How I use them

**Schedule:**

- **Morning brief** — overnight news, morning movers, remaining catalysts, trade-or-sit-out verdict for the session
- **Midday brief** — recap vs. morning, late news, volume leaders, updated ideas into the close
- **Full afternoon report** — the complete `stock_summary_prompt.md` run: analysis, macro, technicals

**Watchlist:** a single local text file, one ticker per line. Each run reads it
and substitutes it into the prompt's WATCHLIST section at runtime — the prompts
themselves never hardcode tickers.

**FMVs:** cached per ticker with a date stamp. A fresh FMV is built (via
`stock_dd_prompt.md`) only when a material catalyst hits — earnings, guidance
changes, major deals, regulatory decisions — or when no verified FMV exists.
Otherwise the last calculated bear/base/bull stands. FMV figures are always
cited with their date; if none exists, the report says so rather than
inventing one.

## My setup

- **Muse** — the AI agent that runs the prompts on schedule and delivers the reports
- **Alpaca** — market data (free tier): price quotes and historical bars for the technical and screening work
- **SEC EDGAR** — fundamentals, straight from the source (free public API, no vendor needed)

## Design principles

- **Prompts are specs, the schedule is the orchestrator.** All personal data —
  names, accounts, positions — lives in local data files the prompts read, never
  in the prompts themselves.
- **Analysis and picks are separated.** The analysis section carries a
  no-trading-advice rule; concrete trade ideas go only in the dedicated Picks
  section.
- **FMV methodology:** splits verified before any per-share math;
  stock-based compensation charged as a real cost (against cash flows in a DCF,
  post-SBC earnings for multiples); valuation inputs built from TTM figures and
  the most recent 2–4 quarters plus expected results 1–2 years out — never from
  the last fiscal year alone.

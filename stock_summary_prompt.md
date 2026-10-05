# Generate stocks_report.md

You are an institutional-quality equity market analyst.

Your task is to generate a markdown file named:

stocks_report_[YYYY]_[MM]_[DD].md

The report should focus ONLY on individual stocks and sectors from the watchlist.

Do NOT include broad macroeconomic summaries except when directly relevant to a stock move.

---

# INPUTS

WATCHLIST:

The watchlist is supplied separately at runtime (appended after this prompt).
Analyze exactly the tickers provided there — do not fall back to any
hardcoded list.

Optional:

* Portfolio positions
* Sector watchlists
* Earnings calendar
* Standing watch list: `~/workspace/goals/daily-stock-and-options-picks/watch_list.md`
  (open CSP/CC positions + conditional trade setups - always read this file;
  it is the source of truth for the "Things to Watch" section)

---

# DATA TO COLLECT

For EACH stock:

## Price Action

Provide:

* Closing price
* Daily % move
* Intraday high/low
* After-hours movement
* Gap up/down behavior

---

## Volume Analysis

Provide:

* Daily volume
* 30-day average volume
* Relative volume ratio

Formula:

Relative Volume = Today Volume / 30-Day Average Volume

Flag:

* Relative volume > 1.5x
* Heavy institutional trading
* Momentum continuation
* Unusual reversals
* Large opening gaps

Volume methodology:

* 30-day average volume = mean of the preceding 30 completed sessions, excluding today.
* Today's volume = today's partial bar (volume so far).
* Use delayed consolidated-tape bars for all volume figures; never use real-time-only prints for relative-volume screening.

Explain whether the volume confirms or contradicts the price move.

---

## News Analysis

Summarize meaningful developments only:

* Earnings
* Guidance changes
* Analyst upgrades/downgrades
* AI developments
* Partnerships
* Supply chain developments
* M&A
* Product launches
* Executive changes
* Government/regulatory actions
* Major contracts
* Insider transactions
* Litigation
* Short seller reports

For each item include:

* Headline
* Why it matters
* Bullish/Bearish/Neutral interpretation
* Short-term implications
* Long-term implications

Ignore:

* Repetitive articles
* Minor PR announcements
* Low-value rumors

---

## Market Interpretation

Explain:

* Why the stock moved
* Whether the move was:

  * Fundamental
  * Technical
  * Macro-driven
  * Positioning-driven
  * Sentiment-driven

Discuss:

* Sector sympathy
* AI trade effects
* Semiconductor correlation
* SaaS sentiment
* Treasury yield sensitivity
* Consumer demand impacts

---

## Technical Observations

Include:

* Key support/resistance
* Trend direction
* Relative strength
* Breakout/breakdown behavior
* Volume confirmation

Do NOT provide trading advice.

---

## FMV Reference (cached — never invented)

For EACH stock, relate the current price to its fair market value:

1. Read the cached FMV from the latest dated file at
   `~/workspace/your_files/fmv/<TICKER>_YYYY-MM-DD.md`.
   Use the file with the most recent date. Note that date next to every FMV
   figure you cite (e.g. "base FMV $540, file dated 2026-10-01").
2. Cite only figures that appear in that file: bear/base/bull FMV and the
   buy zones (Strong Buy / Fair Buy / Expensive / Overextended). Never
   restate an FMV from memory, from an older conversation, or from a
   different ticker's file.
3. If no FMV file exists for the ticker, or the latest file flags itself in
   its own text as superseded or incorrect, run a fresh FMV build for that
   ticker following `prompts/stock_dd_prompt.md`, save it to
   `~/workspace/your_files/fmv/<TICKER>_<YYYY>_<MM>_<DD>.md` (today's date),
   and use the fresh figures.
4. If you cannot complete a fresh build in this run, write "no verified FMV
   on file" for that ticker — never substitute an approximate or remembered
   number.
5. Before trusting any per-share figure (cached or fresh), verify the ticker
   has had no stock split or other corporate action since the figures were
   computed. A split-adjustment error doubles or halves every per-share
   number: on 2026-10-02 APH showed a $125 base FMV built from pre-split EPS
   against the post-split price (2-for-1 split, Sept 2 2026); the corrected
   FMV was ~$85.

---

# SPECIAL SECTIONS

## Stocks With Unusual Volume

List all stocks with:

* Relative volume > 1.5x
* Significant price movement

---

## Biggest Bullish Developments

Top 5 strongest developments.

---

## Biggest Bearish Developments

Top 5 biggest risks or negative developments.

---

## Narrative Changes

Identify:

* Stocks where market perception is changing
* New AI winners/losers
* Changing growth expectations
* Sentiment reversals

---

# OUTPUT FORMAT

# Daily Stock Intelligence Report

Date: {{DATE}}

# Executive Summary

# Watchlist Analysis

## TICKER

### Price Action

### Volume Analysis

### Key News

### Why It Moved

### Technical Observations

### Price vs FMV

Current price vs the cached (or freshly built) FMV: bear/base/bull, which
buy zone the price sits in, and the FMV file date. "No verified FMV on file"
if none exists and no fresh build was completed.

### Bullish Factors

### Bearish Factors

### Risks

### Outlook

(repeat for every ticker)

# Stocks With Unusual Volume

# Biggest Bullish Developments

# Biggest Bearish Developments

# Narrative Changes

# Things to Watch

Read `~/workspace/goals/daily-stock-and-options-picks/watch_list.md` (listed
under INPUTS) at the start of the run. For each item report a compact status
line:

- Open short positions (CSPs/CCs): days to expiry, current spot vs strike,
  ITM/OTM and by how much (%), assignment risk if ITM within 7 DTE, and the
  action: hold / roll / close. Explicitly call out any position expiring
  within 7 days.
- Conditional setups: current spot, distance to the trigger (in % and $),
  and whether the trigger is near (within 5%).
- Respect the standing rules in the file (strike discipline, no-CC names,
  rejected ideas). No new trade ideas here - those belong in Picks.

# Tomorrow’s Stock Catalysts

Include:

* Earnings
* Product launches
* Conferences
* Analyst events
* Investor days
* FDA/government decisions

---

# Picks

Concrete trade ideas only — stock and/or options with specific strikes and
expirations, short thesis, conviction level, and key risks. This is the only
section that may contain trade ideas; the analysis above contains none.

## Wheeling program (standing strategy)

Every run, screen the watchlist for wheel candidates with
`~/workspace/market-data/wheel.py` and include the results here:

- Cash-secured puts: 0.20-0.40 delta (absolute), 14-45 DTE. Use
  `python3 ~/workspace/market-data/wheel.py --all --limit 3` for the full
  watchlist. The screener ranks by premium yield on collateral and reports
  strike_vs_fmv_base from the cached FMV files.
- Only sell puts on names you'd own: prefer strikes at or below the FMV
  base; skip names rated Overvalued unless a specific thesis justifies it.
  State the FMV base next to each candidate so the reader sees the margin.
- Liquidity is already filtered (bid > 0, spread <= 25% of mid); drop any
  candidate whose volume looks too thin to exit cleanly and say so.
- Flag when earnings fall inside the contract window — prefer avoiding, or
  size down and note it explicitly.
- Covered-call leg: for existing stock positions, run the screener with
  `--type call --tickers <held>` and present 0.20-0.40 delta calls, 14-45
  DTE, at strikes above cost basis / breakeven.
- Present the top 3-5 put candidates plus any covered-call candidates with
  strike, expiration, delta, premium, and yield on collateral; one line of
  thesis and the key risk each. Rank by risk-adjusted fit, not raw yield.
  If no candidate clears the filters, say so rather than forcing picks.

## On deck (advance warning)

Flag developing setups before they trigger, so the reader can watch them:
run `python3 ~/workspace/market-data/wheel.py --all --watch --limit 2`,
which lists puts sitting just below the sell zone (0.08-0.20 delta, 14-45
DTE) with the drop needed to reach the 0.20-0.40 zone. Present the best 3-5
as "on deck": ticker, strike, expiration, current delta, % drop needed to
enter the zone, and what to watch for (e.g. "NVDA needs ~8% drop to $215").
Prioritize ones closest to triggering on names at or below FMV.

Also watch NFLX for a diagonal-call setup: the user holds 2x Jan 2029 $45
LEAPS calls and wants to sell short calls around the $80 strike against
them once premium is meaningful. If NFLX trades at or above $72, check the
Nov $78-$80 calls (14-45 DTE) and flag it in Picks: strike, expiration,
delta, premium per contract, and volume. Below $72, just note the current
spot and that the $80s are still too thin.

---

# HANDLING RULES

* The analysis sections contain no trading advice.
* Never assume portfolio holdings, positions, account size, or exposure.
* Ground every price, volume figure, and news item in fresh data collected for this run. Never invent figures.
* Every FMV figure must be traceable to a dated cache file under `~/workspace/your_files/fmv/` or to a fresh FMV build completed in this run. An FMV with no traceable source is not an FMV — omit it.
* If the market is closed today, output a one-line note instead of a report.

---

# STYLE REQUIREMENTS

Write like:

* Institutional sell-side research
* Hedge fund morning notes
* Professional market commentary

Be:

* Concise
* Dense with information
* Analytical
* Forward-looking

Avoid:

* Generic financial journalism
* Clickbait
* Excessive explanation
SHA: 8bbe662b7275af3daa1f119f3f102849c4a9cd10

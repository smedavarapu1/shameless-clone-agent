# Superinvestor 13F signal (Signal D) - data, filters, and evidence

Signal D: tracked "superinvestor" fund managers (value-investing / concentrated-portfolio managers tracked by dataroma.com) newly buying or materially adding to a stock, per their quarterly SEC 13F filing. The slowest-moving of the four signals, but backed by professionally curated, long-track-record investors.

Table of contents:
- Why this signal exists, and its limits
- Data source: dataroma.com
- The filters that isolate signal
- Cluster definition
- Pitfalls

## Why this signal exists, and its limits

- **13F filers are institutional investment managers with ≥$100M AUM**, required by the SEC to disclose long U.S.-equity holdings quarterly. dataroma.com curates ~80+ of these into "superinvestor" profiles - well-known concentrated/value managers (Buffett/Berkshire, Pabrai, Ackman, and similar), not passive index funds.
- **The edge thesis is track record, not speed.** Unlike Signal B (insiders, ~0-2 day lag) or even Signal A (politicians, ~45 day lag), a 13F is filed within **45 days after quarter-end** - so a position can be visible anywhere from same-day (rare, early filers) to ~135 days after the manager actually opened it (a position opened on day 1 of a quarter, filed on day 45 after quarter-end). **This is the most lagged signal in the SOP.** Treat it as evidence of a fund's medium/long-term conviction, not a timely entry signal.
- **Why still worth using:** these managers have long, auditable track records (unlike a member of Congress or a mid-level insider), and a *cluster* of several independently buying the same name in the same quarter is a genuine, hard-to-fake convergence of professional opinion.

## Data source: dataroma.com

dataroma.com is **HTML-only** - no public API, no JSON/CSV export. Its Terms of Service (`/m/inc/tos.php`) explicitly **forbid republishing, reproducing, redistributing, or selling the content**, while allowing citing small portions with attribution + a link back. Operating implication: **fetch it live, per run, don't scrape-and-store a local copy of its dataset; always cite dataroma.com (with a link) when a proposal references it.**

Verified pages (checked 2026-07-16):

- **Quarterly aggregate buy grid:** `https://www.dataroma.com/m/g/portfolio_b.php?q=q&o=c` - stocks bought/added this quarter across all tracked superinvestors, sorted by count (`o=c`) or percentage (`o=p`). Columns: Symbol, Stock, % of portfolio, **Buys** (count of superinvestors with buy/add activity this quarter), Hold Price, Current Price, 52-week range. `?q=q` = last quarter, `?q=h` = last two quarters. **This aggregate count does NOT distinguish "Buy" (new position) from "Add" (increase), and does NOT name which funds are behind the count** - it's a screening/discovery view only, not enough to confirm a cluster by itself.
- **Per-stock drill-down (use this to confirm a cluster):** `https://www.dataroma.com/m/stock.php?sym=TICKER` - shows which named superinvestors hold/recently traded the stock. Use this to identify the actual fund names and each one's activity (Buy/Add/Reduce/Hold) for a candidate ticker surfaced by the aggregate grid above.
- **Per-fund holdings (use this to verify a specific manager's activity):** `https://www.dataroma.com/m/holdings.php?m=FUNDCODE` (e.g. `BRK` for Berkshire Hathaway, `psc` for Pershing Square). Has a "Recent Activity" column reading like `Add 203.99%`, `Reduce 35.17%`, or `Buy`, plus linked Activity/Buys/Sells/History views for that fund. Find fund codes from the alphabetical list at `https://www.dataroma.com/m/home.php`.
- **Grand portfolio (broader cross-check, lower urgency):** `https://www.dataroma.com/m/g/portfolio.php` - aggregate holdings (not just this-quarter activity) across all tracked funds, with a per-stock count of how many hold it. Links to the same `stock.php?sym=` drill-down.
- **Portfolio dates lag the filing:** each fund's page states "Updated <date>" and the holdings reflect the 13F's reporting-period end (a calendar quarter-end), not the filing date itself - always read and state the reporting-period end date, not "today," in any proposal.

## The filters that isolate signal

Apply in order:

1. **Screen** the aggregate buy grid (`portfolio_b.php?q=q&o=c`) for tickers with a rising buy count this quarter.
2. **Drill into each candidate** via `stock.php?sym=TICKER` to get the named funds and their individual activity.
3. **Activity filter (Hard Rule 23).** Keep only rows marked **"Buy"** (brand-new position) or **"Add"** (a meaningful increase, generally >10-15% - read the actual percentage, don't assume). Drop "Reduce," "Sell," and unchanged carry-forward holdings.
4. **Cluster gate (Hard Rule 22).** Keep only tickers with **2+ distinct superinvestors** showing Buy/Add activity in the **same reporting quarter**. Discard single-fund tickers, regardless of how prominent that one fund is.
5. **Freshness gate (Hard Rule 24).** Confirm the reporting-period end date is the most recently completed quarter. If the current quarter's 13Fs are still trickling in, use the latest completed quarter across the whole cluster - don't mix a fresh single-fund filing with a stale one from a prior quarter to manufacture a cluster.
6. Hand the candidate to the SKILL.md **shared gate** (hard rules + stock sanity check + the ≥15%-since-quarter-end chase check, using the quarter-end price as the reference).

## Cluster definition (Signal D)

A valid superinvestor cluster = **2+ different tracked funds**, each showing **"Buy" or "Add" activity** on the **same stock**, in the **same 13F reporting quarter** (calendar quarter-end). A single fund adding twice within a quarter is still one fund - it does not count as two.

## Pitfalls

- **The aggregate grid's "Buys" count can include Reduce/Hold-adjacent noise if misread** - always verify actual Buy/Add activity per named fund on the drill-down page, never trust the grid count alone as a cluster.
- **Portfolio-weight ≠ position size for us.** A fund putting 8% of a $10B portfolio into a stock is an $800M bet; that says nothing about how much we should risk. Standard sizing (Hard Rule 8 + SKILL.md sizing section) always governs (Hard Rule 25).
- **13Fs only cover long U.S.-equity positions** - no shorts, no options, no cash, no non-U.S.-listed holdings are visible. A fund's real portfolio (and hedges) may look very different from what dataroma shows.
- **Staleness compounds with our own lag.** By the time we act on a 13F, the position could be 45-135 days old at filing PLUS whatever time passes before we screen it. Always apply the chase check (Hard Rule 10) using the quarter-end reference price, not the fund's original entry price, since we don't know the exact entry price or date within the quarter.
- **Don't redistribute dataroma's data.** Fetch live each run; cite the source and link in any proposal or log entry rather than storing a persistent local dataset (their Terms of Service).

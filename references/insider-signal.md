# Insider (SEC Form 4) signal - data, filters, and evidence

Signal B: corporate insiders (CEOs, CFOs, directors, 10%+ owners) buying their own company's stock on the open market. This is the stronger, better-documented of the agent's two signals.

Table of contents:
- Why this signal is strong
- Data sources
- Transaction codes (the critical filter)
- The filters that isolate signal
- Cluster definition
- Pitfalls

## Why this signal is strong

- **Documented ~6-10%/yr abnormal return** on insider BUYS, persistent across 25+ years (Lakonishok & Lee 2001; Jeng/Metrick/Zeckhauser 2003; Cohen/Malloy/Pomorski 2012).
- **Buys >> sells.** Insider purchases predict returns; insider sells do NOT (sells happen for taxes, diversification, liquidity, planned 10b5-1). Trade buys only; never short a sell.
- **Short lag = actionable.** Form 4 must be filed within 2 business days of the trade (often same/next day). About 1/3 of the abnormal return is front-loaded in the first month, so the ~0-2 day lag captures most of it - far better than Congress's ~45 days.
- **Opportunistic > routine.** The predictive power lives entirely in discretionary, non-scheduled trades. Routine/planned trades are worthless (Cohen/Malloy/Pomorski). The post-2023 mandatory 10b5-1 checkbox is how we operationalize this.
- **Clusters roughly double the signal** - multiple insiders buying the same name in a tight window.

## Data sources

All three below are **free, no paid key**, and verified current to 2026-06-08 (research pass 2026-06-09). Recommended live flow: **discover code-P clusters on openinsider, then confirm each hit + read the 10b5-1 flag on EDGAR.** FMP is an optional third opinion.

- **Primary (free, ground truth): SEC EDGAR Form 4 XML.** The ONLY source that carries the 10b5-1 checkbox - the aggregators do not, so this read is mandatory, not optional.
  - **Discover recent Form 4s:** daily index `https://www.sec.gov/Archives/edgar/daily-index/2026/QTR2/` (swap year/quarter). The `form.idx` lists every filing; grep form type `4`. The `index.json` confirmed newest entry 2026-06-08.
  - **Or full-text search:** `https://efts.sec.gov/LATEST/search-index?q="Rule 10b5-1"&forms=4&startdt=YYYY-MM-DD&enddt=YYYY-MM-DD` returns JSON (`hits.hits[]._id` = `ACCESSION:filename`, `._source.ciks[]`, `._source.file_date`). The `q=` param cannot be empty - pass a keyword, or use the daily index for an unfiltered sweep.
  - **Build the XML URL:** `https://www.sec.gov/Archives/edgar/data/{cik_int}/{accession_no_dashes}/{filename}`. Parse Table I (non-derivative); read `<transactionCode>` (the P/A/M/F/S filter) and **`<aff10b5One>`** (value `1` = planned 10b5-1, drop it). Footnote text is a second 10b5-1 signal.
  - **CRITICAL gotcha:** EDGAR returns **403 to any request without a `User-Agent: Name email@domain` header.** This is why a plain WebFetch fails on EDGAR - set a descriptive UA in the agent's HTTP client. ~10 req/sec courtesy limit. No key.
- **Discovery (free, current): OpenInsider cluster-buys.** `http://openinsider.com/latest-cluster-buys` - effectively a pre-filtered code-P *cluster* feed (verified ~101 "P - Purchase" rows vs 2 sells; fresh to 2026-06-08). The **`Ins` column = number of distinct insiders = our cluster size**, handed to us with no parsing. Other useful screens: `/insider-purchases`, `/screener?xp=1&grp=1` (`xp=1` purchases, `grp=1` clustered).
  - **No clean CSV/JSON export** (the `/CSV` path is read as a ticker; `dumpcsv=1`/`fmt=csv` return HTML). Parse the HTML table (fixed columns, `P - Purchase` is a literal match) OR use the maintained scraper **`sd3v/openinsiderData`** (last release 2025-11-27; CSV/Parquet, filters by "P - Purchase").
  - **No 10b5-1 flag** here - openinsider gives you the cluster fast, but you MUST confirm each hit on EDGAR for the planned-trade flag before trusting it.
- **Optional cross-check: FMP free tier** (250 req/day; insider endpoints e.g. latest-insider-trade, search-insider-trades) and/or **Finnhub** (`/stock/insider-transactions`, free signup key, 100 txns/call, per-symbol only). Both confirm a specific ticker's insider history but **neither exposes the 10b5-1 flag** - EDGAR remains the authority.
- **API key:** FMP key in project `.env` as `FMP_API_KEY` (see data-sources.md). EDGAR + OpenInsider need no key. (Finnhub if used: free key in `.env` as `FINNHUB_API_KEY`.)

## Transaction codes (the critical filter)

Read the **transaction code**, never just the Acquired/Disposed flag. Table I codes:

- **P = open-market or private PURCHASE** → the ONLY code that counts as a conviction buy.
- **S = open-market SALE** → noise. Never a trigger, never a short.
- **A = grant/award/acquisition from the company** (RSUs, comp) → NOT a buy; the insider didn't spend cash. Exclude.
- **M = exercise/conversion of a derivative** (option exercise) → mechanical; often paired with a sale. Exclude.
- **F = shares withheld for tax/exercise cost** → mechanical disposition. Exclude.
- **G = bona fide gift** → no signal. Exclude.
- C, D, X, J → other; exclude for our purposes.

## The filters that isolate signal

Apply in order to raw Form 4 data:

1. **Code = P only.** Drop everything else (Table II derivatives too).
2. **Exclude 10b5-1 = true.** Keep only discretionary/opportunistic buys (read the mandatory checkbox, present since Feb 27, 2023).
3. **Cluster:** keep only tickers with **2+ distinct insiders buying within ~10 trading days** (3+ is stronger). Single-insider buys are dropped (Hard Rule 15).
4. **Meaningful size:** require a real spend (rough floor ~$50k-$100k absolute) AND/OR a meaningful % increase in the insider's existing stake (check Column 5, post-transaction holdings). Drop token buys.
5. **Role as a weight, not a gate:** CEO/CFO buys carry conviction, but opportunistic director/non-exec buys are often equally or more informative - capture them too. Do not restrict to C-suite.
6. Then hand the candidate to the SKILL.md **shared gate** (hard rules + stock sanity check). Note: our ≥$5 / ≥$2B-cap rule trims the small-caps where the academic edge is strongest - that is an accepted safety tradeoff, not an oversight.

## Cluster definition (Signal B)

A valid insider cluster = **2+ different insiders** (distinct people, not the same person twice) at the **same company**, each with a **code-P open-market purchase**, **not 10b5-1**, with **transaction dates within ~10 trading days** of each other. Bipartisan has no meaning here; the analog of "bipartisan" strength is **role diversity + count** (e.g. CEO + CFO + a director buying together beats two directors).

## Pitfalls

- **Option-exercise-and-sell (M then S)** looks like activity but is just liquidating comp. Filter out.
- **Grants (A)** masquerade as buys if you key on the A/D flag instead of the code. Always check the code.
- **10%-owner automatic filings:** funds crossing ownership thresholds generate Form 4s that aren't conviction trades. Weight down pure 10%-owner buys vs officer/director buys.
- **Gifts/inheritance (G):** zero signal.
- **Direct vs indirect ownership + footnotes** change economic meaning; sanity-check Column 5 before sizing conviction.
- **Penny/illiquid names:** the literature's biggest edge is in micro/small-caps we deliberately exclude (≥$5, ≥$2B). Stay in mandate even though it costs some edge.

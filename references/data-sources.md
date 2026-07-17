# Data sources - finding politician trades

This file covers the POLITICIAN signal (Path A). For the INSIDER signal (Path B - SEC Form 4 data, EDGAR, transaction codes), see insider-signal.md. For the SUPERINVESTOR 13F signal (Path D - dataroma.com), see superinvestor-signal.md. For the prediction-market signal (Path C), see prediction-markets.md.

Table of contents:
- Primary source (FMP)
- Real-time cross-check sources (Unusual Whales + QuiverQuant)
- How to pull each on a live run
- Cross-checks (manual)
- Backstop (official filings)
- Data reality check

## Primary source: Financial Modeling Prep (FMP), free tier

Clean JSON, both chambers, updated daily. Free tier = 250 calls/day - poll once or twice daily.

- Senate latest: `https://financialmodelingprep.com/stable/senate-latest?apikey=YOUR_KEY`
- House latest: `https://financialmodelingprep.com/stable/house-latest?apikey=YOUR_KEY`
- By member name: `.../stable/senate-trading?name=...` and `.../stable/house-trading-by-name?name=...`
- Historical prices (for performance scoring): FMP historical-price endpoint, or `yfinance` (free).

**API key:** stored in the project `.env` (git-ignored) as `FMP_API_KEY`. Any helper script must load it with an inline `load_env()` that walks up to the nearest `.env` - never hardcode the key.

**Fields per record:** politician name, chamber, ticker, transaction type (purchase/sale), amount range (the disclosure bracket), trade date, disclosure date.

**Limitation:** FMP returns RAW TRANSACTIONS ONLY - no performance/return field. Per-politician returns must be computed by us (see performance-scoring.md).

## Real-time cross-check source (FREE - Congress Trading Monitor JSON)

**No paid APIs.** Sai does not have (and does not want to pay for) Unusual Whales or QuiverQuant keys. The good news: a single **free, no-auth** source gives us everything those paid feeds were going to - lag flags AND pre-computed per-trade excess return. Use it as the primary live cross-check against FMP on every run.

### Congress Trading Monitor (kadoa-org) - committed static JSON, no key
Repo: `github.com/kadoa-org/congress-trading-monitor` (MIT). The maintainers scrape the **free official sources** (House Clerk FD portal, Senate eFD, OGE) on their side and **commit the normalized result as static JSON** to the repo. We just fetch the raw JSON - no Kadoa account, no API key, no pipeline to run. (Kadoa is only their internal collection tool; it is not required to consume the data.)

- **Primary feed (fetch this):** `https://raw.githubusercontent.com/kadoa-org/congress-trading-monitor/main/public/data/trades.json`
  - Try `/master/` if `/main/` 404s. Companion files in the same `public/data/` dir: `filers.json`, `tickers.json`, `stats.json`, `returns.json`, `prices.json`, plus per-`filer/` and per-`ticker/` drill-downs.
- **Per-trade fields (verified 2026-06-09):** `transaction_date`, `filing_date`, `owner` (Self/Spouse/Joint), `ticker`, `asset_name`, `asset_type` (Stock/ST/OP/...), `transaction_type` ("Purchase" / "Sale (Full)" / "Sale (Partial)"...), `amount_range_low`, `amount_range_high`, `amount_range_label` (bracket), `days_to_file`, `is_late` (0/1), `notification_received_over_30d`, `filer_id`, `filer_name`, `branch`, `chamber` (house/senate), `party` (R/D/I), `state`, `agency`/`office` (for executive-branch filers), `doc_url` (link to the original filing PDF), `filing_type`, **`ret_since`, `excess_since`, `ret_30d`, `ret_1y`** (per-trade return + benchmark-excess return).
- **Why this is the whole win:** `is_late` / `days_to_file` / `notification_received_over_30d` give the disclosure-lag + late-report flags we wanted from Unusual Whales. `excess_since` / `ret_since` give the per-trade alpha we wanted from QuiverQuant's `ExcessReturn`. Plus `party`, `chamber`, `state`, and a `doc_url` straight to the source filing. **All free.** It also covers executive-branch filers (`branch`/`agency`), which FMP does not.
- **Freshness caveat (the one real risk):** this is a committed snapshot, refreshed only when the maintainers push. Verified current to `filing_date` 2026-06-07 on 2026-06-09 (good), but **do not assume daily updates.** On every run, read the max `filing_date` in the file and report it. If it is more than ~5 days stale, say so and lean on FMP (which updates daily) as the primary, using this only as a secondary cross-check.
- **Asset-type filter:** the feed includes options (`asset_type` OP) and non-stock assets. Per Hard Rule 3, **drop anything that is not common stock / ETF** before clustering - never substitute the underlying for a disclosed option.

### Paid sources we are NOT using (reference only)
Both are JS-rendered dashboards whose data sits behind a paid Bearer/Token key. **We do not pull these** (no keys). Listed only so a future paid upgrade is documented, and so nobody wastes a run trying to scrape the empty rendered page:
- Unusual Whales `unusualwhales.com/politics?view=flow` - API `GET api.unusualwhales.com/api/congress/recent-trades` (Bearer, paid). Adds `transaction_date`/`filed_at_date` lag split. *Kadoa JSON already gives us this for free.*
- QuiverQuant `quiverquant.com/congresstrading/` - API `GET api.quiverquant.com/beta/live/congresstrading` (`Authorization: Token`, paid). Adds per-trade `ExcessReturn`/`SPYChange`. *Kadoa JSON's `excess_since` already gives us this for free.*
- If Sai ever buys a key, store it in `.env` as `UNUSUALWHALES_API_KEY` / `QUIVER_API_KEY` (via `load_env()`, never hardcoded) and promote it back to a live cross-check.

## How to pull on a live run (free path)

When the skill is invoked ("run the trading agent" / "check politician trades"), do this in order and **delegate the network-heavy pulls to a sub-agent** to protect the main context:
1. Pull **FMP** House + Senate latest (primary daily feed - updates every day).
2. Pull the **Congress Trading Monitor `trades.json`** (free cross-check; brings `is_late` + `excess_since`). Record its max `filing_date` and flag if stale (>~5 days).
3. **Reconcile:** a ticker appearing in **both** FMP and the Kadoa feed is a stronger cluster signal than one feed alone. A trade in FMP but missing from a *fresh* Kadoa file (or a wildly different bracket) is a flag to investigate, not to act on. Use `excess_since` to sanity-check that a cluster's members aren't all negative-alpha names before clustering.
4. Drop options / non-common-stock rows (Hard Rule 3) and then run the normal cluster gate + performance disqualification.
5. If the Kadoa file is unreachable or stale, **fall back to FMP-only and say so in the proposal** - never silently skip the cross-check, and never report "no trades" off a failed/empty fetch.

## Cross-checks (manual / human-facing, not automated)

Use to sanity-check a signal before acting, especially clusters - these are eyeball-only (no free API):
- capitoltrades.com/trades - best human-facing tracker; per-politician performance charts.
- congress.kadoa.com - the hosted dashboard for the same JSON above (handy for a quick human look).
- The Unusual Whales + QuiverQuant **web dashboards** are fine for a human eyeball cross-check - just remember they are paid for programmatic pulls and JS-rendered, so the agent does NOT scrape them.

## Backstop (free, authoritative, heavy)

Official filings - free, no key, the upstream source FMP/Kadoa themselves scrape. Use to verify a surprising signal AND as a daily freshness ground-truth, not as the parsed daily feed (trade detail needs PDF parsing).

- **House (CONFIRMED current + the best free official House feed): the Clerk's annual bulk ZIP.**
  - Fetch: `https://disclosures-clerk.house.gov/public_disc/financial-pdfs/2026FD.zip` (swap year). Plain HTTPS GET, no key, no ToS gate. Verified `Last-Modified: 2026-06-08`, 241 Periodic Transaction Reports YTD, newest filing 2026-06-06/08 (research pass 2026-06-09). The Clerk republishes this ZIP daily as filings arrive.
  - Contains `<year>FD.xml` (index) + `.txt`. Per-filing fields: `FilingType` (**P = Periodic Transaction Report** = the stock trades), `FilingDate`, `Last`/`First`/`Prefix`, `StateDst`, `DocID`. The trade-level detail (ticker, amount range, buy/sell) lives in the per-filing PDF at `https://disclosures-clerk.house.gov/public_disc/ptr-pdfs/<YEAR>/<DocID>.pdf` - **needs OCR/text-extraction**, which is the one friction point vs pre-parsed FMP/Kadoa.
  - **Use it as:** a free authoritative freshness check (does FMP/Kadoa's newest House filing match the Clerk's?) and a verifier for a surprising House cluster - not the primary parsed feed (the PDF step is heavy).
- **Senate: efdsearch.senate.gov - NO free bulk file.** It is all behind an agreement-cookie + Django CSRF handshake (POSTing `/search/report/data/` without it returns 403). Scriptable but fragile + ToS-gray. There is no static Senate ZIP analogous to the House. **Lean on FMP/Kadoa for Senate coverage**; use eFD for manual one-off verification only.

## Data reality check

- All amounts are **ranges** (e.g. $1K-15K, $50K-100K, $1M-5M), never exact.
- Disclosures lag the actual trade by **up to 45 days**.
- Treat everything as a delayed, fuzzy signal. No provider gives a timing edge over the disclosure window. (The Kadoa feed's `is_late`/`days_to_file` just make the lag *visible* - they don't shrink it.)
- **Never scrape the rendered Unusual Whales / QuiverQuant web pages** - they are JS shells, return empty to a plain fetch, and need a paid key for their API. We use the free Kadoa JSON instead.
- Avoid as primary dependencies: senate/house-stock-watcher (stale/broken as of 2026), Finnhub congressional endpoint (paid + ticker-scoped), scraping CapitolTrades (no API, against ToS).

## Evaluated and REJECTED (do not re-investigate)

Checked these on 2026-06-09 so a future run doesn't waste time re-evaluating them:
- **`timothycarambat/senate-stock-watcher-data`** - REJECTED: abandoned. Data stops at **Dec 2020**, last commit Mar 2021; Senate-only; missing party/state/filing-date. Its "continuously updated" README claim is false. Strictly redundant with FMP + Kadoa and ~5.5 yrs staler. (Raw file `…/master/aggregate/all_transactions.json` does still load if ever needed for a 2012-2020 Senate backfill only.)
- **`Quiver-Quantitative/python-api`** - REJECTED for free use: it is only a thin auth'd wrapper for the **paid** QuiverQuant API ($30/mo `Authorization: Token`, no free tier, zero bundled data, no unauthenticated endpoint). Useless without a paid key; adds nothing over the free Kadoa `excess_since`. Only revisit if Sai buys a Quiver subscription.

Additional sources checked + rejected on 2026-06-09 (politician + general):
- **CapitolTrades** (`capitoltrades.com`) - no sanctioned free API. Its undocumented `bff.capitoltrades.com/trades` JSON backend is ToS-gray AND was returning **CloudFront 503** during the check. MCP wrappers (`anguslin/mcp-capitol-trades`, `AyaanIqbal/Capitol_Trades_API`) just scrape the rendered HTML and are 7-17 months stale. Avoid for a production agent.
- **Finnhub congressional-trading endpoint** - PAID (premium alt-data), not on the free tier. (Finnhub's *insider* endpoint is free-key, but lacks the 10b5-1 flag - see insider-signal.md.)
- **Quiver via QuantConnect** - PAID ($5/mo dataset add-on), and only usable inside QC algorithms. Skip.
- **Alpha Vantage / Benzinga / Hugging Face** - no free, current congressional-trade dataset found.
- **Dead GitHub repos** (confirmed by commit date, all claim "updated daily" falsely): `pastrosd/Congress-Trades` (2023), `semerriam/congress-stock-trades` (2022), `sime-time/congress-trade-tracker` (2023), `noodleslove/...` (housestockwatcher API now 403). `manosp2/Congressional-Trade-Monitor` + `kbarker223/congressional-research` look recent but ship NO committed data (scraper app / research paper). `johnisanerd/Apify-...` needs the paid Apify platform. None worth wiring in.

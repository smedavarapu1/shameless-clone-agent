# Politician performance scoring - follow winners, not the loudest

Table of contents:
- Why we score
- The hard truth about free data
- How to compute each member's score
- Window weighting
- Bucketing
- Accuracy caveats (state these honestly)
- Manual cross-check
- Verified leaderboard (populated by validation research)

## Why we score

We score politician performance to **disqualify** members whose disclosed trades carry no real signal - not to chase "stars." Trade *frequency* is not a signal; a member with 1,000 trades a year is noise. A single lucky held position is not skill. The validation below shows the durable-alpha set is nearly empty, so the score's main job is removing dead weight from clusters (Hard Rule 14).

## The hard truth about free data

No pre-computed *per-member* return data is free and machine-readable. Quiver's per-member **leaderboard** (CAGR, win rate, Sharpe) is paid; Unusual Whales publishes per-member returns only once a year; CapitolTrades is website-only. FMP/Finnhub return raw transactions with zero performance field.

(Exception worth using - FREE: the Congress Trading Monitor `trades.json` carries a **per-trade** `excess_since` / `ret_since` / `ret_30d` / `ret_1y` on each transaction - see [data-sources.md](data-sources.md). That is a free live cross-check on whether a cluster's members are positive-alpha, but it is per-trade, not the per-member multi-year bucket this file computes. Use it to sanity-check buckets, not to replace them. The equivalent paid feeds, Quiver's `ExcessReturn` / UW returns, are NOT used - no keys.)

So **the agent computes a rough performance score itself** from free data. The goal is **directional bucketing (strong / average / weak), NOT precise returns** - that's all we need to follow winners and screen out laggards.

## How to compute each member's score (free path)

1. Pull the member's full trade history from FMP (House/Senate by-name endpoints).
2. Build a hypothetical mirror portfolio: enter each *buy* at the **disclosure date** (not trade date - earliest a copier could actually act), size at the **amount-range midpoint**, mark to market with free historical prices (FMP historical or yfinance), exit on a disclosed matching sale or mark-to-today if still held.
3. Compute return over each window: **3yr, 2yr, 1yr, YTD, 30-day**. Compare to SPY over the same window - **excess return** is what matters, not raw return in a bull market.
4. Apply window weighting (below).
5. Bucket the member (below). Refresh roughly monthly - performance scores don't move fast.

## Window weighting (Sai: favor longer windows)

- Weight **3yr and 2yr heaviest, 1yr next.**
- Treat **YTD and 30-day as minor tiebreakers only.**
- Consistency across years beats a single hot streak. A politician good over 3 years outranks one who got lucky in the last 30 days.

## Bucketing

- **STRONG** - beats SPY across most weighted windows.
- **AVERAGE** - roughly matches SPY.
- **WEAK** - lags SPY / negative. Never followed (Hard Rule 14).
- **UNRATED** - fewer than ~10 disclosed trades. Treat as AVERAGE-with-caution; never the sole basis for a buy. A 3-trade lucky streak is not skill.

## Accuracy caveats (never present the score as precise)

- **Amount ranges** → midpoint sizing is crude; consider equal-weighting if midpoints distort badly.
- **45-day lag** → enter at disclosure date or the score overstates what a real copier captures.
- **Options can't be priced from disclosures** → exclude them from the score. This understates options-heavy traders (e.g. Pelosi) - fine, since we don't trade options anyway, but flag such members as NOT equity-replicable.
- **Small samples lie** → enforce the ~10-trade minimum (UNRATED below it).
- **Spouse/managed accounts** → attribute carefully; a member who says they don't personally trade (e.g. trades via a spouse's trust) still counts, but note it.

## Manual cross-check (once a year)

Sanity-check the agent's STRONG/WEAK buckets against Unusual Whales' free annual Congress Trading Report and CapitolTrades profile charts. If buckets wildly disagree with theirs, investigate before trusting the score.

## Performance is a DISQUALIFIER, not a star-picker

A deep multi-source validation pass (46-agent cross-check + adversarial review, 2026-06-07) reached a blunt conclusion: **the "follow top-performing politicians" edge mostly does not survive scrutiny.** Use the leaderboard to **EXCLUDE** members who add no signal - not to chase "stars."

Why the stars are mostly mirages:
- **~90% of famous return figures are single-source** (Unusual Whales' annual report, re-syndicated by Benzinga/Motley Fool/Finviz). Repetition is not confirmation.
- **Almost every "star" is one lucky held position**, not skill. Strip the single name (NVDA, GE Vernova, one gold miner) and the edge evaporates.
- **Trade-date vs disclosure-date gap is fatal:** headlines measure from the trade date; our agent can only buy after the 45-day disclosure. Realized copy-returns are far lower; for late filers the move is gone.
- **Academia doesn't agree Congress beats the market** (Ziobrowski yes; Eggers & Hainmueller and Belmont no).

## How to apply the buckets

- **WEAK / EXCLUDE / no-live-signal members do NOT count toward a cluster** (Hard Rule 14). This is the primary use of the leaderboard.
- **Eligibility floor before any member counts as STRONG/AVERAGE:** ≥10 diverse disclosed trades AND more than one return-driving position AND ≥2 independent return methodologies. Below the floor = UNRATED (counts as AVERAGE-with-caution, never the sole basis for a buy).
- **Trust confidence labels literally:** only HIGH-confidence buckets are decision-grade. Note most HIGH-confidence calls here are HIGH-confidence UNRATED/EXCLUDE - i.e. high confidence there is nothing to copy.
- **Never fade a negative return.** The "worst performers" are single-position or retirement-account artifacts, not short opportunities.
- **The cluster + bipartisan signal remains the primary driver.** Performance only removes members; it does not, by itself, justify a buy.

## Validated leaderboard (2026-06-07) - treat mostly as a disqualification list

| Member | Bucket | Conf. | Counts toward a cluster? | Why |
|---|---|---|---|---|
| Nancy Pelosi (D-CA) | STRONG | MED | YES - equity book only | Only durable multi-year equity edge (~21% CAGR equity-clone vs SPY ~13%). Ignore options headline. **Signal ends Jan 2027** (not running). |
| Debbie Wasserman Schultz (D-FL) | STRONG* | LOW | NO | +142% is one gold-miner disclosed 14mo late - un-mirrorable. |
| Roger Williams (R-TX) | AVERAGE | MED | Caution | 2024 tech windfall that mean-reverted; recent timing negative. |
| Pete Sessions (R-TX) | AVERAGE | LOW | Caution | Legit 2024, no 2025 follow-through, rotated to Treasuries. |
| Marjorie Taylor Greene (R-GA) | AVERAGE | LOW | NO | **Resigned Jan 5, 2026** - no forward signal. |
| Susan Collins (R-ME) | WEAK | MED | NO | Third-party adviser-managed, no edge; munis. |
| Dan Crenshaw (R-TX) | WEAK | HIGH | NO | No trades since 2023; returns are beta. Lame-duck. |
| Warren Davidson (R-OH) | WEAK | HIGH | NO | Zero 2025 trades; one held GE lot. |
| Donald Norcross (D-NJ) | WEAK | MED | NO | 2-name backtest artifact. |
| Terri Sewell (D-AL) | WEAK | MED | NO | Two trades total; NVDA beta. |
| Bob Latta (R-OH) | WEAK | MED | NO | One hometown bank stock at a loss. |
| David Rouzer (R-NC) | UNRATED | HIGH | NO | #1 in 2024 but ~17 lifetime trades, none since 2022. |
| Ron Wyden (D-OR) | UNRATED | MED | NO | Spouse's 2020 NVDA buy; effective decisions ~1. |
| Morgan McGarvey (D-KY) | UNRATED | HIGH | NO | Fully divested; nothing to mirror. |
| Larry Bucshon (R-IN) | UNRATED | HIGH | NO | **Retired Jan 2025**; n=1 SPAC, break-even. |
| Bryan Steil (R-WI) | UNRATED | LOW | NO | Static legacy basket; ~0 active trades. |
| Chip Roy (R-TX) | UNRATED/EXCLUDE | HIGH | NO | -59% is spouse RSU comp, not a pick. Don't fade. |
| Jim McGovern (D-MA) | UNRATED | LOW | NO | One falling stock; not a strategy. |
| Pete Stauber (R-MN) | UNRATED | LOW | NO | One unlucky trade; skip, don't fade. |
| Aaron Bean (R-FL) | UNRATED | HIGH | NO | Loss is a fund drawdown, not trades. |
| Nellie Pou (D-NJ) | UNRATED | LOW | NO | Freshman, 0 PTRs; holdings snapshot only. |

\* Schultz is "STRONG historically" but un-mirrorable in practice - treated as NO for cluster purposes.

**Bottom line:** of 21 validated names, **only Pelosi's equity book is mirror-worthy, and it expires Jan 2027.** Everyone else is EXCLUDE or UNRATED on a per-signal basis. The agent should lean on the **cluster + bipartisan** signal and use this table to remove dead weight - not to find heroes. Trading nothing is better than mistaking one held NVDA/GE lot for a strategy.

## Full accuracy warnings + refresh cadence

The detailed data-accuracy warnings (single-source contamination, range imputation, attribution errors, survivorship) and the refresh schedule live in [validation-findings.md](validation-findings.md). Re-read before re-rating any member.

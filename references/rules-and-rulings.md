# Rules rationale, account scope, and rulings

Table of contents:
- Account & scope
- Operating philosophy
- Hard-rule rationale (the non-obvious ones)
- Rulings log (decisions that became rules)
- Position sizing & portfolio shape

## Account & scope

- **Broker:** Robinhood agentic trading account (Sai Medavarapu), connected via MCP endpoint `https://agent.robinhood.com/mcp/trading`.
- **Funded capital:** $2,000 total. **Initial deployment tranche: ~$100** - the rest sits in cash until Sai chooses to deploy more. The 35% single-position cap (Hard Rule 8) is computed against the full $2,000 baseline, so it isn't the binding constraint yet at $100 deployed; the ~$35-50 per-position target below is what actually governs today.
- **Fractional shares:** Use them. Buy fractions of expensive stocks rather than skipping them.
- **Cash buffer:** Keep ≥5% of the deployed tranche in cash. Never spend the active tranche to zero.
- **Asset reality:** Robinhood agentic trading is long-equities-only at launch (options rolling out; no crypto/futures/margin yet) - which aligns with our guardrails. Verify current asset availability before relying on anything beyond long equities.

## Operating philosophy

- **Time horizon:** Weeks to a few months. Not high-frequency, not intraday.
- **Trade frequency:** Low. A few trades per week at most. Doing nothing is valid and often correct - real signal is rare (see performance-scoring.md).
- **Priorities:** Discipline over excitement. Diversification over conviction bets. Explainable trades over clever ones. A clean, auditable paper trail for every decision.
- **Avoid:** hype names with no cluster, FOMO/chasing, averaging down a loser, any position that can't be explained in one sentence.
- **Conviction = explainable.** Every buy traces to a specific, dated, in-window cluster - either members who survived the disqualification filter (Signal A) or insiders making open-market non-10b5-1 buys (Signal B). "It's going up" is not a reason.

## Hard-rule rationale (the non-obvious ones)

- **Rule 3 (no options, no underlying substitution):** disclosures often list options ambiguously and they can't be priced reliably; substituting the underlying changes the risk profile the politician actually took. Drop the signal cleanly.
- **Rule 10 (no chasing, >15% since trade date):** the 30-45 day disclosure lag means the easy move is often already gone. If the market already ran, the edge is spent.
- **Rule 12 (cluster mandatory):** a single member can trade for idiosyncratic reasons (liquidity, a spouse's managed account, tax). Multiple independent members buying the same name in a tight window is a far stronger, harder-to-fake signal.
- **Rule 14 (no weak performers):** the politician strategy assumes congressional buys carry signal. A member who consistently underperforms the market carries negative signal - following them is worse than indexing.
- **Rules 15-17 (insider signal):** insider BUYS are a documented ~6-10%/yr edge (Lakonishok & Lee; Jeng/Metrick/Zeckhauser; Cohen/Malloy/Pomorski), but ONLY for discretionary open-market purchases. Rule 16 (code P only) strips out grants/exercises that aren't conviction. Rule 17 (no 10b5-1) strips out pre-planned routine trades that the research shows are worthless. Rule 15 (cluster) mirrors the politician cluster logic and roughly doubles the signal. Insider SELLS are excluded entirely because selling is noise (taxes/diversification/planned). Full detail: insider-signal.md.

## Rulings log (decisions that became rules)

Recorded 2026-06-07 from a live Phase-1 approval round:

- **Approved** the bipartisan UnitedHealth (UNH) cluster at small $1K-15K brackets → bipartisan clusters are a buy even at small size. (Rule 12.)
- **Rejected** Gottheimer's solo 4-ticker semis basket (MU/ADI/LITE/FN) → one member is never enough, no matter how many tickers. (Rule 12.)
- **Rejected** McCaul's larger-bracket ($15K-50K) solo Teradyne buy → bigger bracket does not substitute for a cluster; discount high-frequency traders. (Rule 13.)
- **Rejected** Pelosi's options book + stale AllianceBernstein buy → no options, no underlying substitution, and signals past the 45-day window are dead. (Rules 3, and the 45-day window.)
- **Performance layer (ruling):** follow politicians with the best returns, not those who trade most; compute performance ourselves (free), favor longer windows. (Rule 14.)
- **Performance is a disqualifier, not a star-picker (ruling, after deep validation 2026-06-07):** a 46-agent cross-source validation showed almost no member produces durable, mirror-able alpha (numbers are single-source, distorted by one lucky position, and gutted by the disclosure lag). So the leaderboard is used to REMOVE no-signal members from clusters, not to chase stars. Only Pelosi's equity book clears the bar, and it expires Jan 2027. (See performance-scoring.md + validation-findings.md.)
- **Added insider (Form 4) as a second independent signal (ruling 2026-06-07):** research showed corporate insider open-market BUYS are a stronger, better-documented, far-less-lagged edge than congressional trades. Sai's calls: two independent signals (either fires a buy; both = highest conviction); insider bar = cluster (2+ insiders, ~10 trading days) + code P + non-10b5-1. Encoded as Hard Rules 15-17, BUY Path B, and the dual-signal ranking. (See insider-signal.md.)
- **Added superinvestor 13F clusters as a fourth independent signal (ruling 2026-07-16):** dataroma.com's ~80+ tracked "superinvestor" managers give a free, professionally-curated signal with none of the data-quality problems that plague the politician signal (see validation-findings.md) - but it is the most lagged signal in the SOP (13Fs file up to 45 days after quarter-end). Sai's calls: cluster bar = 2+ distinct funds, Buy/Add activity only (never Reduce/Sell/unchanged), same reporting quarter; standard position sizing regardless of the fund's own portfolio weighting. Encoded as Hard Rules 22-25, BUY Path D. (See superinvestor-signal.md.)
- **Relative (SPY-adjusted) stop-loss, not an absolute one (ruling 2026-07-16):** Sai does not want a broad market crash alone to force an exit on every position. The -15% stop now requires the stock to also be underperforming SPY by ≥10 points over the same window; if SPY itself is down ≥10% over that window, the automatic stop is suspended and the position gets a manual stock-specific review instead. Encoded in the SELL decision process (SKILL.md + decision-process.md).

## Position sizing & portfolio shape

- **Target: 2-3 positions**, spread across the ~$100 tranche using fractional shares. Diversified, not concentrated.
- **Per-position target: ~$35-50** at entry. Never more than **35% of the total funded $2,000 account** (~$700) in one stock - not currently binding at this tranche size, but stays meaningful as more capital gets deployed over time.
- **One stock per company.** No adding to a winner beyond the 35% cap; no doubling down on a loser, ever.
- **Sector spread:** avoid 3+ positions in the same sector. Cap any single sector at ~50% of the portfolio.
- **Keep ≥5% cash** at all times.
- When a position is sold, freed cash returns to the candidate pipeline - don't rush to redeploy; wait for a real cluster.

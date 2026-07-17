# Approval workflow - worked examples

Real, dated trades showing the exact proposal/skip format for all four signal paths. Use this style for live proposals. Always pull fresh data at decision time and verify against the live feed - names/dates below are illustrative.

## Phase 1 proposal format

Ticker · action · $ amount · signal type (politician / insider / superinvestor / prediction-market / combination) · signal detail (members + perf buckets + brackets + dates; and/or insiders + roles + code P + dates; and/or fund names + activity + reporting quarter; and/or print + implied prob + anchor + divergence) · one-sentence thesis · explicit rule check · then ask "Approve, reject, or modify?"

## Examples

**1 - Cluster buy, disqualification-screened (the only kind of buy we make):**
> **Proposal: BUY ~$40 of UnitedHealth (UNH).** Three members bought UNH inside ~10 days: Ro Khanna (D-CA, $1K-15K, 4/13/2026), Gilbert Cisneros (D-CA, $1K-15K, 4/14), Maria Elvira Salazar (R-FL, two buys $1K-15K, 4/22). Bipartisan, same ticker, tight window - clears the cluster gate. Disqualification check: none of the three is on the EXCLUDE/WEAK list, ≥2 qualifying members remain. NYSE, mega-cap, >$5 - passes hard rules. **Approve, reject, or modify?**

**2 - Single-member basket → no trade (one member is never enough):**
> **Skip (no proposal):** Gottheimer (D-NJ) bought MU + ADI + LITE + FN (all $1K-15K, filed 6/3). One member. Fails the cluster gate - no second member confirms any ticker. **No trade.**

**3 - Bigger bracket, famous trader, still solo → no trade:**
> **Skip (no proposal):** McCaul (R-TX) bought Teradyne (TER) at $15K-50K (4/28) - bigger bracket than the UNH cluster, but single-member, and McCaul is a high-frequency trader. Fails the cluster gate; size never overrides it. **No trade.**

**4 - Options + stale → no trade (no options, no underlying, signals past 45 days are dead):**
> **Skip (no proposal):** Pelosi's January book is mostly call options (GOOGL/AMZN/NVDA) - we don't trade options or substitute the underlying. Her one stock buy (AB, $1M-5M, 1/16) is ~5 months stale and single-member. **No trade.** (Note: Pelosi's *equity* book is the one validated STRONG signal, but only when it appears as a fresh, in-window, qualifying cluster member - not as a stale solo options filing.)

**5 - Cluster killed by the disqualification filter (teaches Rule 14):**
> **Skip (no proposal):** Three members bought the same ticker in-window - a surface cluster. But the leaderboard rates two of them WEAK/no-live-signal (e.g. a fully-divested member + a one-lucky-position member). After dropping them, fewer than 2 qualifying members remain. **No trade** - we don't mimic members whose disclosures carry no signal, even in a crowd.

**6 - Sell triggered by a politician selling a held name:**
> **Proposal: SELL our full Nvidia (NVDA) position.** Sen. Whitehouse (D-RI) disclosed two NVDA sales of $100K-$250K each (5/8/2026). Signal reversal against a name we hold; position up ~12%. **Approve the sell?**

**7 - Profit-target sell (rules-driven, no signal needed):**
> **Proposal: SELL our full UnitedHealth (UNH) position.** Up 26%, past the 25% take-profit rule. **Approve?**

## Insider-signal (Path B) examples

**8 - Insider cluster buy (the only kind of insider buy we make):**
> **Proposal: BUY ~$40 of [Ticker]. Signal: insider cluster.** Three insiders made open-market purchases (code P) within ~8 trading days: CEO (~$220k, 5/19), CFO (~$95k, 5/21), and a director (~$60k, 5/26). All code P, none flagged 10b5-1. Role-diverse cluster - strong. NYSE, >$5, >$2B cap - passes hard rules. **Approve, reject, or modify?**

**9 - Single insider, big buy, still no trade:**
> **Skip (no proposal):** One director bought ~$400k of [Ticker] (code P, non-10b5-1, 5/22). Genuine open-market buy, but a single insider. Fails the insider cluster gate (Rule 15) - size never substitutes for a cluster. **No trade.**

**10 - Looks like buying, but it is grants/exercises → no trade:**
> **Skip (no proposal):** [Ticker] shows three insider "acquisitions" this week, but all are code A (RSU grants) and code M (option exercises), not open-market purchases. None is a code-P conviction buy (Rule 16). **No trade** - this is comp, not conviction.

**11 - Multi-signal (highest conviction):**
> **Proposal: BUY ~$50 of [Ticker]. Signal: MULTI (politician + insider).** Politician cluster (2 members, bipartisan, $15K-50K, late April, both off the EXCLUDE list) AND an insider cluster (CEO + CFO open-market code-P buys, non-10b5-1, early May). Two independent signals point to the same name - top-ranked setup. Passes hard rules. **Approve, reject, or modify?**

**Note on insider SELLS:** never propose a sell because an insider sold. Insider selling is noise and is not a trigger.

## Superinvestor-signal (Path D) examples

**12 - Superinvestor cluster buy (the only kind of Path-D buy we make):**
> **Proposal: BUY ~$40 of [Ticker]. Signal: superinvestor cluster.** dataroma.com Q2 2026 13Fs (reporting period ended 6/30/2026, filed by 8/14): two tracked funds show fresh activity - Fund A "Buy" (new position, 3.1% of portfolio) and Fund B "Add" (+28% to an existing stake). Both confirmed via the per-stock drill-down, not just the aggregate grid. Cluster gate clears (2 distinct funds, same quarter). NYSE, >$5, >$2B cap, not up >15% since quarter-end - passes hard rules. **Approve, reject, or modify?**

**13 - One famous name, still no trade:**
> **Skip (no proposal):** [Fund] disclosed a new position in [Ticker] this quarter - a well-known, prominent manager. But no second tracked superinvestor shows Buy/Add activity on the same stock this quarter. Fails the cluster gate (Rule 22) - one fund is never enough, regardless of reputation. **No trade.**

**14 - Reduce mistaken for a buy → no trade:**
> **Skip (no proposal):** The aggregate grid showed [Ticker] with a rising "Buys" count, but drilling into the named funds shows one is actually a "Reduce 18%" and the other is an unchanged carry-forward holding from three quarters ago. No genuine Buy/Add activity this quarter (Rule 23). **No trade** - this was a screening false positive, not a cluster.

**15 - Stale quarter, don't backfill a cluster:**
> **Skip (no proposal):** Fund A shows a fresh "Buy" this quarter (period ended 6/30/2026). Fund B's only activity on the same ticker is from two quarters ago (period ended 12/31/2025) - it has since gone unchanged. Mixing quarters to manufacture a cluster isn't allowed (Rule 24). Only one fund is current. **No trade.**

## SELL examples - relative stop-loss

**16 - Absolute -15% during a broad selloff → stop suspended, not auto-sold:**
> **Flag (not an automatic sell): [Ticker] is down 16% from cost.** But SPY is also down 11% over the same window - this looks like broad-market beta, not a stock-specific breakdown. Per the market-crash pause, the automatic stop is suspended. No fresh negative signal on the underlying cluster/insiders. **Recommend: HOLD. Confirm or override?**

**17 - Stock-specific breakdown → stop fires:**
> **Proposal: SELL our full [Ticker] position.** Down 17% from cost; SPY is roughly flat over the same window (-1%) - this is idiosyncratic underperformance, not market beta. Relative stop-loss triggers (Rule: -15% cost AND ≥10pt SPY underperformance). **Approve the sell?**

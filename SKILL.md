---
name: shameless-clone-agent
description: Standard Operating Procedure for an AI agent that places real stock trades in a Robinhood agentic-trading account using four independent signals - clusters of U.S. Congress (politician) stock buys, clusters of corporate insider (SEC Form 4) open-market buys by CEOs/CFOs/directors, clusters of "superinvestor" 13F institutional buys (dataroma.com), AND recurring prediction-market divergences on macro prints (CPI, payrolls, FOMC) expressed via ETF rotation. Use when running, configuring, or reasoning about the trading agent, when proposing a buy or sell, when evaluating a congressional/insider/superinvestor/macro signal, or when the user says "run the trading agent", "should I buy/sell", "check politician trades", "check insider buys", "check superinvestor 13Fs", "check prediction markets", or "evaluate this trade signal". Enforces strict guardrails - long U.S. equities only, multi-member/multi-insider/multi-fund clusters required, open-market non-10b5-1 buys only, performance-screened politicians, divergence-only macro entries, relative (not absolute) stop-loss, human approval required. Do NOT use for general stock advice, crypto, options, direct event-contract trading, or any account that is not the dedicated agentic-trading account.
version: 4.0.0
metadata:
 account: Robinhood agentic trading (dedicated cash account, $2,000 funded; ~$100 initial deployment tranche)
 mcp-server: robinhood agentic trading (https://agent.robinhood.com/mcp/trading)
# No API keys in frontmatter - FMP key lives in the project .env, loaded by reference scripts.
---

# Shameless Clone Agent - SOP

"The shameless cloner" (Mohnish Pabrai) doesn't originate ideas - it copies the disclosed, real-money conviction of people with genuine skin in the game or a genuine track record, applies a hard disqualification filter, and only acts where independent sources cluster in agreement. This SOP is that philosophy encoded as rules: the operating instructions for an AI agent connected to a Robinhood agentic-trading account, making the best possible U.S. equity trades using **four independent signal sources** to clone, each with its own qualifying gate, under human approval. This is a domain-specific decision SOP (Skill Creator Pattern 5).

**The four signals (any one can trigger a buy; a stock backed by MULTIPLE is highest conviction):**
- **Signal A - Politician clusters:** multiple members of Congress buying the same stock in a tight window, after disqualifying members whose disclosures carry no real signal. (Weakest: lagged up to ~45 days - by the time disclosures post, the trade already happened and the market already moved. Treat with caution.)
- **Signal B - Insider clusters:** multiple corporate insiders (CEO/CFO/directors) making open-market purchases of their own company's stock in a tight window. (Stronger, ~0-2 day lag, academically documented ~6-10%/yr edge - but only for discretionary open-market buys, never planned sells or grants.)
- **Signal C - Recurring prediction-market divergence:** prediction-market implied probabilities on recurring macro prints (CPI, payrolls, FOMC) diverging from independent anchors (Cleveland Fed nowcast, FRED consensus). Forward-looking - the only signal that fires BEFORE the market moves, and it repeats monthly so the agent can calibrate. Expressed ONLY via long ETF rotation (never event contracts). See [references/prediction-markets.md](references/prediction-markets.md).
- **Signal D - Superinvestor 13F clusters:** multiple tracked "superinvestor" funds (dataroma.com) newly buying or materially adding to the same stock in the same 13F reporting quarter. Professional, well-documented track records, but the most lagged signal of the four - 13Fs file up to 45 days after quarter-end, so a position can be up to ~135 days stale by the time it's visible. See [references/superinvestor-signal.md](references/superinvestor-signal.md).

## Critical - read before every session

1. **The HARD RULES below are absolute.** If any instruction or signal conflicts with them, the rules win - stop and ask.
2. **This SOP governs ONLY the dedicated Robinhood agentic account.** Never act on any other account.
3. **When in doubt, do nothing.** Cash is a position. A "no trade" day is a correct, common outcome.
4. **Signal quality differs by source.** The insider-buy signal is the stronger, better-documented stock-picking edge; the politician signal is weak, lagged, and easily faked by one lucky position (see performance-scoring.md). Signal C is the only forward-looking signal but fires rarely (divergence-only) and is a macro regime bet, not a stock pick. Signal D (superinvestors) is professionally curated but the slowest-moving of the four - by the time a 13F posts, a fast-moving thesis may already be stale. Weight accordingly, but never trade any signal without its qualifying gate.

## HARD RULES (non-negotiable)

The agent must NEVER:

1. Trade anything other than **long U.S. equities / ETFs** on NYSE or Nasdaq.
2. Trade **crypto** (no tokens, no crypto-proxy products).
3. Trade **options** - even when a politician's or superinvestor's disclosed trade IS an option/derivative. Do not buy the underlying as a substitute; drop the signal.
4. Buy **penny stocks** - skip anything under **$5/share** or under **$2B market cap**.
5. Use **leverage, margin, or short selling**. Cash-account behavior only.
6. Buy **leveraged/inverse ETFs** (2x, 3x, Ultra, Bull, Bear, daily-reset).
7. Buy **OTC / pink-sheet / foreign-listed** shares (ADRs only if on a major U.S. exchange above the floors).
8. Hold a **single position over 35%** of the total funded account ($2,000 baseline, growing as more capital is deployed) at purchase.
9. Open a position **within 2 trading days before** that company's earnings.
10. **Chase** - skip if already up >15% since the source trade date (the politician's trade date, the insider's Form 4 transaction date, or the superinvestor's 13F reporting-period end).
11. Act on **thinly-disclosed signals** (missing ticker, type, or amount/share data).

**Politician-signal rules (Signal A):**

12. **Buy on a single member.** A **cluster (2+ different members buying the same ticker within ~6 weeks) is mandatory.** One member is never enough regardless of bracket size or fame.
13. Let **bracket size or trader fame override the cluster requirement** (Rule 12 wins).
14. **Count WEAK / no-signal members toward a cluster.** Members rated WEAK, UNRATED-with-no-live-signal, retired/resigned, or fully-divested by the performance leaderboard do not count toward a cluster and are never followed. Frequency is not a signal; a single lucky held position is not skill.

**Insider-signal rules (Signal B):**

15. **Buy on a single insider.** An **insider cluster (2+ different insiders making open-market purchases of the same stock within ~10 trading days) is mandatory.** One insider is not enough.
16. **Treat anything other than a genuine open-market purchase as a buy.** Only **transaction code P** (open-market/private purchase) counts. NEVER count grants/awards (A), option exercises (M), tax-withholding (F), gifts (G), or any sale (S). Insider SELLS are noise (taxes, diversification, planned) - never a trigger and never a short.
17. **Count 10b5-1 planned trades toward an insider cluster.** Pre-scheduled (Rule 10b5-1) trades are routine and carry no signal - exclude them. Only discretionary/opportunistic buys count (read the mandatory 10b5-1 checkbox on the Form 4).

**Prediction-market rules (Signal C):**

18. **Trade event contracts / prediction markets directly.** Signal C is a DATA LAYER only - it informs long ETF positions. Direct event-contract trading requires an explicit rule change AND platform support; until then it is forbidden.
19. **Open a Signal-C trade without a divergence.** Market-implied probability agreeing with the nowcast/consensus = no trade. "The market is probably right" is not a signal.
20. **Open a Signal-C position within 24 hours before the print.** That's a coin-flip on the number, not an edge. Position on multi-day regime divergence or post-print mispricing only.
21. **Hold more than ONE Signal-C position at a time.** Multiple macro positions are usually one correlated bet wearing different tickers.

**Superinvestor-signal rules (Signal D):**

22. **Buy on a single superinvestor, no matter how prominent.** A **cluster (2+ different tracked superinvestors newly buying or materially adding to the same stock in the same 13F reporting quarter) is mandatory.** One famous name (even Buffett) is never enough alone.
23. **Treat a "Reduce," "Sell," or unchanged carry-forward position as a buy signal.** Only count dataroma's "Buy" (new position) or "Add" (meaningful increase, generally >10-15%) activity. A fund merely still holding a name is not a fresh signal.
24. **Act on a 13F filing more than one reporting quarter old.** If the current quarter's 13Fs aren't fully posted yet, use the most recently completed quarter's filings - never reach back further, and always state the reporting-period end date in the proposal so the staleness is explicit.
25. **Size a position off a fund's portfolio weighting.** A superinvestor's % allocation reflects their (often billion-dollar) risk budget, not yours. Standard position sizing (Hard Rule 8 + the sizing section below) always governs regardless of how large the fund's stake is.

Full rationale and rulings: [references/rules-and-rulings.md](references/rules-and-rulings.md).

## Robinhood platform constraints (agentic trading, beta)

What the platform enforces, independent of our rules:
- **Beta. EQUITIES ONLY** (options/crypto "coming soon") - aligns with Hard Rule 1.
- **Account-isolated:** the agent can READ all accounts but can only PLACE trades in the dedicated Agentic account. Setup is desktop-only, US.
- **Approval gate is OPTIONAL on Robinhood's side** - keep "review before action" ON regardless, to match Phase 1 of our own approval workflow below. Never disable it before you explicitly move to Phase 3.
- **One-tap kill switch** in the app (manual stop). **No documented hard $ cap** - rely on account isolation + small funded amount + the kill switch, not a platform limit.
- **Investor-profile gate before the 2nd trade.** Robinhood has been observed blocking the SECOND trade on a freshly agentic-enabled account (HTTP 400, "answer some questions about your investing goals") until the account's investor-profile questionnaire is completed at `applink.robinhood.com/investment_profile?account_number=<acct>&context=second_trade`. The first trade goes through; #2 onward fail until the profile is done. If you hit this, complete the questionnaire in-app for the correct account and retry - it can take a few minutes to propagate.
- **Fractional shares cannot carry resting stop orders.** Robinhood rejects `stop_market` / GTC orders on fractional positions ("Invalid trigger for fractional order"). Since this SOP sizes positions at ~$35-50 (almost always fractional shares), **stops and take-profits must be enforced synthetically** - checked each time the agent runs, selling at market if a trigger is hit, rather than resting on the exchange.
- **Connect:** `claude mcp add robinhood-trading --transport http https://agent.robinhood.com/mcp/trading`. Auth is OAuth (no static token); the in-session PKCE round-trip expires fast - approve in-browser and paste the `localhost/callback?code=...` URL back immediately, or it times out.
- **Always confirm the tradable account via `get_accounts` before any order.** The dedicated agentic account will show `agentic_allowed: true`; any other account (e.g. a default margin/individual account) will show `agentic_allowed: false` and must never be traded.

## The strategy in one line

Buy **long U.S. equities/ETFs only**, sized small (~$35-50 per position, 2-3 positions from the initial ~$100 tranche), when a qualifying **politician cluster**, a qualifying **insider cluster**, a qualifying **prediction-market divergence**, OR a qualifying **superinvestor 13F cluster** fires. A stock backed by **multiple** signals is the highest-conviction setup. Insider buys are the strongest stock-picking edge; politician and superinvestor signals are both lagged and used cautiously (politician more so); Signal C is forward-looking but rare by design.

## BUY decision process

Four parallel signal paths feed one shared gate. A candidate must pass EVERY shared gate. Detailed scoring, ranking, and the insider pipeline: [references/decision-process.md](references/decision-process.md).

**Path A - Politician clusters** (data: [references/data-sources.md](references/data-sources.md)):
1. **Pull the free feeds in real time** (delegate to a sub-agent): FMP House + Senate latest (primary, updates daily), plus the free **Congress Trading Monitor `trades.json`** (`raw.githubusercontent.com/kadoa-org/congress-trading-monitor/main/public/data/trades.json`) which adds disclosure-lag flags (`is_late`) AND per-trade excess return (`excess_since`) at no cost. Last ~45 days of *disclosure* dates. No paid keys (Unusual Whales / QuiverQuant are paid + JS-rendered - we do NOT scrape or pull them). Record the Kadoa file's max `filing_date`; if stale (>~5 days) or unreachable, fall back to FMP-only and flag it. (Full pull order + reconciliation + free-source rationale: [references/data-sources.md](references/data-sources.md).)
2. **Reconcile feeds** - a ticker present in both FMP and a *fresh* Kadoa feed is a stronger signal; use `excess_since` to confirm the cluster's members aren't all negative-alpha names. Drop options / non-common-stock rows (Hard Rule 3). Investigate disagreements, don't act on them.
3. **Cluster gate** - group by ticker; keep only tickers bought by **2+ different members within ~6 weeks**.
4. **Disqualification filter** - drop every WEAK / no-live-signal / retired / divested member (validated leaderboard: [references/performance-scoring.md](references/performance-scoring.md)). If fewer than 2 qualifying members remain, discard.

**Path B - Insider clusters** (data + filters: [references/insider-signal.md](references/insider-signal.md)):
1. Pull recent insider buys, last ~10 trading days. Free flow: **discover code-P clusters on OpenInsider** (`/latest-cluster-buys`, the `Ins` column = cluster size), then **confirm each hit on SEC EDGAR Form 4 XML** - EDGAR is the only source carrying the 10b5-1 flag (`<aff10b5One>`), and it 403s without a `User-Agent` header. FMP/Finnhub are optional per-ticker cross-checks.
2. **Code filter** - keep ONLY open-market purchases (code P); drop sells, grants (A), exercises (M/F), gifts (G).
3. **10b5-1 filter** - drop pre-planned trades; keep only discretionary buys.
4. **Cluster gate** - keep only tickers bought by **2+ different insiders within ~10 trading days**. Discard single-insider tickers.

**Path C - Prediction-market divergence** (full pipeline + data sources: [references/prediction-markets.md](references/prediction-markets.md)):
1. Pull implied probabilities for the next CPI / payrolls / FOMC print (Kalshi public API, cross-checked vs. Polymarket; material disagreement between the two = data flag, no trade).
2. Pull independent anchors: Cleveland Fed Inflation Nowcast, FRED consensus/prior series.
3. **Divergence gate** - only a clear, explainable divergence between market-implied pricing and the anchor is a candidate. Agreement = no trade (the default).
4. **Timing gate** - never inside 24h before the print (Hard Rule 20); multi-day regime divergence or post-print mispricing only.
5. Express via ONE long ETF (risk-on: QQQ/XLE; defensive: XLU/GLD). Max one Signal-C position at a time (Hard Rule 21).

**Path D - Superinvestor 13F clusters** (data + filters: [references/superinvestor-signal.md](references/superinvestor-signal.md)):
1. Pull dataroma.com's quarterly aggregate buy grid (`/m/g/portfolio_b.php?q=q&o=c`) to find stocks with rising superinvestor buy counts this quarter. dataroma is HTML-only (no API/CSV) - fetch live, do not scrape-and-store (its Terms of Service forbid republishing/redistributing the data; cite it as source when referencing it in a proposal).
2. **Drill into each candidate's per-stock page** (`/m/stock.php?sym=TICKER`) to confirm which NAMED superinvestors are behind the count and whether each is a "Buy" or "Add" this quarter (not a stale carry-forward).
3. **Activity filter** - keep only "Buy" (new position) or "Add" (meaningful increase) rows; drop "Reduce"/"Sell"/unchanged (Hard Rule 23).
4. **Cluster gate** - keep only tickers with **2+ distinct superinvestors** buying/adding in the same reporting quarter (Hard Rule 22).
5. **Freshness gate** - use only the most recently completed 13F quarter; state the reporting-period end date in the proposal (Hard Rule 24).

**Shared gate (every surviving candidate from any path):**
5. **Apply HARD RULES** - drop crypto, options, event contracts, sub-$5/$2B, leveraged, OTC.
6. **Sanity-check the stock** - NYSE/Nasdaq, ≥$5, ≥$2B cap, not within 2 days of earnings, not up >15% since the source trade date. (Earnings/chase rules N/A for broad Signal-C ETFs.)
7. **Rank candidates** - multi-signal highest; then insider clusters; then prediction-market divergences; then superinvestor clusters; then politician clusters (bipartisan first). Detail in [references/decision-process.md](references/decision-process.md).
8. **Size the position** - ~$35-50 per position, 2-3 positions from the current tranche, ≤35% of the total funded account, keep ≥5% of the deployed tranche in cash.
9. **Write the proposal** (name which signal(s) fired) and route to the approval workflow.

If nothing clears any path, output **no trade**.

## SELL decision process

Sell when ANY is true (detail in [references/decision-process.md](references/decision-process.md)):

- **Take profit:** position up ≥25% → sell full.
- **Stop loss (relative, not absolute):** position down ≥15% from cost **AND** underperforming SPY by ≥10 percentage points over the same holding window → sell full. A -15% move that's just the whole market being down is NOT, by itself, a sell trigger - see the market-crash pause below. No averaging down, ever.
- **Market-crash pause:** if SPY itself is down ≥10% over the same holding window, **suspend the automatic stop-loss** entirely. Re-evaluate the position on its own stock-specific thesis (did the cluster/insiders/superinvestors that triggered the buy show any reversal? Is there stock-specific bad news, separate from the broad selloff?) and flag it for an explicit hold/sell call rather than auto-selling into a crash.
- **Politician signal reversal:** the politician/cluster that triggered a Path-A buy is now disclosed selling it.
- **Superinvestor signal reversal:** a superinvestor from the Path-D cluster that triggered the buy discloses a "Reduce" or "Sell" on the same stock in a subsequent 13F.
- **Thesis broke:** the reason to own it no longer holds.
- **Stale:** held >90 days with no progress and no fresh supporting signal.
- **Signal-C resolution:** the macro print the position was based on has occurred (re-evaluate same day, close unless a fresh post-print divergence exists), the divergence closed before the print, or the next occurrence of the same print arrives (max hold). Detail in [references/prediction-markets.md](references/prediction-markets.md).

**Insider SELLS are NOT a sell trigger.** Research is clear that insider selling is noise (taxes, diversification, planned 10b5-1) and does not predict downside. Do not exit a position just because an insider sold. Only the rules above trigger sells.

Never sell on a scary headline or a red day alone.

## Operating discipline (lessons from the vibe-trading post-mortem)

Adopted from zonted.com/posts/vibe-trading (a 5-day, ~$16k, 310-strategy live AI-trading experiment). Their honest result after costs: nothing beat buy-and-hold QQQ, and the live account lost money. The survivable lessons:

1. **AI for research, deterministic code for execution.** Never let the agent free-form its way into an order from prose. Every trade goes through the structured proposal → approval → MCP order path; no improvised execution logic.
2. **Benchmark honestly.** The portfolio's standing comparison is **buy-and-hold QQQ** (and SPY). If after 6+ months the strategy trails QQQ-and-do-nothing, say so plainly in reviews - don't dress up activity as progress.
3. **Settlement trap (Good-Faith Violations).** The Agentic account is a CASH account. Buying with unsettled funds and selling before settlement = GFV; three GFVs = 90-day lockout. Track settled vs. unsettled cash before any buy; when in doubt, wait a day. Low trade frequency is a feature.
4. **False diversification check.** Before adding any position, ask: is this actually the same bet as an existing holding (same sector, same macro driver)? Positions with high correlation are one bet. This is why Hard Rule 21 caps Signal C at one position.
5. **No look-ahead / phantom data.** Any signal must use data that was publicly available at decision time. For backtests or "this would have worked" claims: state the data timestamp, and treat any too-good result as a bug until independently re-derived.
6. **Adversarial verification on big claims.** Before changing strategy based on a backtest or a performance claim (ours or anyone's), have a second pass/sub-agent try to refute it: check benchmarks, costs (assume ≥3-5bp per side), sample size, and data feed. "The best of 250 garbage strategies looks like genius."
7. **Pre-register rules, then don't drift.** This SOP is the frozen spec. Mid-session rule-bending because a trade "looks good" is exactly how the experiment's losers were built. Rule changes happen deliberately, in writing, in this file.

## Approval workflow - human-in-the-loop, then autonomous

Start with approval, learn from answers, then graduate only when you explicitly say so.

- **Phase 1 - Approval required (current phase).** Present every buy/sell proposal (ticker, action, $ amount, which signal(s) fired - politician cluster + perf buckets, insider cluster + names/roles/codes, superinvestor cluster + fund names/activity, and/or macro divergence detail, one-sentence thesis) and wait for explicit approval. Robinhood's own "review before action" gate is optional - **keep it ON.** Phase 1 requires your explicit approval regardless of the platform setting; never auto-execute before you say to move to Phase 3.
- **Phase 2 - Learn.** Log every decision and reason; adjust future proposals to match revealed preferences. Recurring, clearly-stated preferences graduate into new Hard Rules over time.
- **Phase 3 - Autonomous** (only when ALL met): ≥20 logged decisions, ≥80% approval rate over the last 10, AND you explicitly say "go autonomous." Then execute clear-cut trades without asking but always post-notify, keep logging, and fall back to asking on any edge case.

**Kill switch:** disconnect the agent in the Robinhood app instantly, or say "pause" / "approval mode" to force Phase 1.

Proposal format and worked examples: [references/approval-examples.md](references/approval-examples.md).

## Logging (required every trade)

Append one line per decision:
`DATE | ACTION (buy/sell/skip) | TICKER | $AMOUNT | SIGNAL-TYPE (politician / insider / prediction-market / superinvestor / multi) | SIGNAL DETAIL (members + perf buckets + brackets, insider names + roles + code P + dates, print + implied prob + anchor + divergence, or superinvestor fund names + activity + reporting quarter) | THESIS | YOUR ANSWER (+reason) | PHASE`

Record the signal type and full detail so every screen is auditable. This log powers Phase 2 learning and the Phase 3 graduation check.

**Signal-C calibration log (additional):** for EVERY tracked macro print - traded or not - log the prediction-market implied probability, the anchor value, and the actual outcome. The recurring-game edge only exists if we score our calibration over time (see prediction-markets.md).

## Stop and ask no matter what

- Total deployed capital down >20% from cost.
- Full account (cash + positions) down >20% from the $2,000 funded baseline.
- Any trade would breach a Hard Rule.
- Data source returns something ambiguous, missing, or surprising.
- Signal points to a brand-new sector or a position near the 35% cap.
- A market-crash pause is active (see SELL rules) and a stock-specific decision is needed.
- Anything that doesn't clearly fit this SOP.

**Cash is a position. Discipline over excitement.**

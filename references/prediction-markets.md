# Signal C - Recurring Prediction Markets (Macro Prints)

Added 2026-06-10 from a Bernard Huang (Clearscope) conversation + the zonted.com "vibe trading" experiment.

## Why this signal exists (the edge thesis)

Signals A and B are **lagging**: by the time a politician disclosure (up to ~45 days) or even a Form 4 (~0-2 days) posts, the trade already happened and the market has at least partially moved. Prediction markets on **recurring macro prints** are **forward-looking** and repeat on a fixed calendar:

- They're **recurring games** (CPI monthly, payrolls monthly, FOMC 8x/yr, jobless claims weekly), so the agent can build a track record, calibrate, and improve - unlike one-off event bets.
- The inputs are **public, triangulatable data** (FRED series, nowcasts, consensus estimates) - an LLM agent's comparative advantage is synthesizing many public sources fast, not out-speeding quants on execution.
- Retail prediction-market prices are often set by **less sophisticated flow** than equity options; mispricings vs. nowcasts persist longer than equity mispricings do.

## What the agent actually does (two modes)

### Mode 1 - ACTIVE NOW: prediction markets as a DATA LAYER for ETF rotation
Stay 100% inside the Hard Rules (long U.S. equities/ETFs only). Use prediction-market implied probabilities as a macro-regime input, expressed via plain ETFs:

1. **Pull implied probabilities** for the next CPI print, payrolls print, and FOMC decision (sources below).
2. **Pull the independent anchors:** Cleveland Fed Inflation Nowcast (CPI), FRED consensus/prior series, recent jobless-claims trend (payrolls).
3. **Look for divergence.** No divergence between market-implied and nowcast/consensus = NO TRADE (the default outcome). Only a clear, explainable divergence (market pricing meaningfully hotter/cooler than the nowcast) is a candidate signal.
4. **Express via ETFs, never event contracts:** risk-on (QQQ, XLE) vs. defensive (XLU, GLD, short-duration via cash) rotation. Long-only, no inverse/leveraged funds (Hard Rule 6).
5. **Same shared gate as Paths A/B/D:** sizing ~$35-50, ≤35% of the total funded account, ≥5% of the deployed tranche in cash, earnings-window rule N/A for broad ETFs.
6. **Timing guard:** never open a Mode-1 position inside the 24 hours BEFORE the print (that's a coin-flip bet on the number itself, not an edge). Position on multi-day regime divergence, or after the print when the market under/over-reacts vs. the prediction market's pre-print pricing.

### Mode 2 - NOT ACTIVE: trading event contracts directly
Robinhood offers event contracts (Kalshi-powered), but (a) the agentic MCP account is **equities-only** in beta, and (b) Hard Rule 1 forbids it. Direct prediction-market trading requires an **explicit rule change from Sai** plus platform support. Until both exist, Mode 2 is documented-but-forbidden. Do not improvise around this.

## Data sources (all free)

| Source | What | Access |
|---|---|---|
| Kalshi public API | Implied prob. for CPI, payrolls, Fed funds markets | `https://api.elections.kalshi.com/trade-api/v2/markets` (no auth for public market data) |
| Polymarket Gamma API | Cross-check implied probabilities | `https://gamma-api.polymarket.com/markets` |
| Cleveland Fed Inflation Nowcast | Independent CPI anchor | clevelandfed.org (scrapeable page; updates daily) |
| FRED API | CPI/payroll history, HY credit spreads (BAMLH0A0HYM2), Fed balance sheet | Existing FRED access; key in project `.env` if needed |
| BLS release calendar | Print dates/times (CPI ~8:30 AM ET, NFP first Friday) | bls.gov/schedule |

Cross-check Kalshi vs. Polymarket: if the two markets disagree materially on the same event, that's a data-quality flag - investigate, don't trade.

## The macro calendar (recurring games)

- **CPI:** monthly, ~8:30 AM ET (BLS schedule).
- **Nonfarm payrolls:** first Friday monthly, 8:30 AM ET.
- **FOMC rate decision:** 8 scheduled meetings/yr, 2:00 PM ET.
- **Initial jobless claims:** weekly Thursday 8:30 AM ET (input, not a trade trigger).

## Guardrails specific to Signal C

1. **Divergence or nothing.** Agreeing with consensus is not a signal; "the market is probably right" = no trade.
2. **Log the forecast vs. outcome every print**, even with no trade taken. This builds the calibration record that justifies (or kills) the signal - the recurring-game advantage only exists if we score ourselves.
3. **Max one Signal-C position at a time** (it's a macro regime bet; multiple "different" macro positions are usually one correlated bet - see vibe-trading lesson on false diversification).
4. **Signal C never overrides A/B sells** and never justifies breaching any Hard Rule.
5. **Same approval workflow:** Phase 1 human approval applies. Signal C proposals must state: the print, market-implied probability, the independent anchor value, the divergence, the ETF expression, and the exit plan.

## Exit rules for Signal-C positions

- Thesis resolves (the print happens): re-evaluate same day; close unless a fresh post-print divergence exists.
- Divergence closes before the print: exit (the edge is gone).
- Standard stops still apply: -15% stop, +25% take-profit (broad ETFs will rarely hit these; the print resolution is the usual exit).
- Max hold: through the next occurrence of the same print. No stale macro positions.

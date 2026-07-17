# Shameless Clone Agent — RESUME (for a fresh chat)

Last updated: (not yet run). Copy the prompt at the bottom into a new chat to continue the loop once trading has started.

## Current state (snapshot)
- **Account:** Robinhood Agentic CASH account — fill in the account number (last 4 + full number) after connecting via `get_accounts`. NEVER trade any account where `agentic_allowed: false`.
- **Approval phase:** Phase 1 - every buy/sell requires Sai's explicit approval. No autonomy override is active.
- **Portfolio:** $2,000 funded. $0 deployed so far — nothing bought yet.

## Live positions (entry → synthetic stop / take-profit)

_None yet. Once a position is filled, add a row here: ticker, shares, cost basis, relative stop reference (SPY level at entry), take-profit level, and which signal(s) triggered it. Fractional positions can't carry resting stop orders on Robinhood, so stops/take-profits are enforced synthetically — checked and acted on each time the agent runs._

| Ticker | Shares | Cost | Stop (relative to SPY, see SKILL.md) | Take-profit | Signal |
|---|---|---|---|---|---|
| — | — | — | — | — | — |

## Standing config
- **Signals live:** politician clusters (A), insider clusters (B), prediction-market divergence (C), superinvestor 13F clusters (D). Full rules in SKILL.md.
- **Sizing:** ~$35-50 per position, 2-3 positions from the current $100 tranche, ≤35% of the full $2,000 account, ≥5% of the deployed tranche in cash.
- **Stop-loss:** relative to SPY — only fires at -15% cost AND ≥10pt SPY underperformance over the same window; suspended entirely if SPY itself is down ≥10% over that window (manual review instead).
- **Loop cadence:** not yet started. Loop runs only while a session is live — no overnight/unattended monitoring.

## Hard limits (never waived)
Long US equities/ETFs only; no options/crypto/leverage/penny/<$5/<$2B/OTC; ≤35% of the $2,000 account per position; ≥5% of the deployed tranche in cash; no chase >15% since source signal date; no earnings within 2 trading days; cluster requirement (2+) for politician, insider, AND superinvestor signals; EDGAR code-P + non-10b5-1 required for every insider buy; Buy/Add-only + same-quarter required for every superinvestor buy.

---

## COPY THIS INTO A FRESH CHAT:

```
Resume the shameless clone agent. FIRST read these for full state:
- RESUME.md (current positions, stops, config — start here)
- SKILL.md (the SOP + hard rules)
- trade-log.md (full history)

Trade ONLY the Robinhood Agentic cash account (confirm via get_accounts — agentic_allowed: true).
NEVER touch any other account. Phase 1: every trade needs Sai's explicit approval before
it executes — do not auto-execute.

Then run one loop tick now: get_portfolio + get_equity_positions + get_equity_quotes on any
held tickers, confirm cost-basis + stops match RESUME.md, check relative stops/take-profits
against current SPY, and screen for fresh signals across all four paths (politician / insider /
prediction-market / superinvestor). If the market is closed, give an end-of-day summary instead.
Log every tick to trade-log.md.
```

# Shameless Clone Agent

An AI trading agent, built as a [Claude Code](https://claude.com/claude-code) Skill, that places real trades in a **Robinhood agentic-trading account** by cloning the disclosed, real-money conviction of people with actual skin in the game — members of Congress, corporate insiders, and top-performing fund managers — instead of generating its own stock picks.

Named after Mohnish Pabrai's "shameless cloning" philosophy: don't originate ideas, copy the best ones you can verify, and only act when independent sources agree.

> ⚠️ This is a personal, opinionated SOP for a small, dedicated, real-money account. It enforces strict guardrails (no options, no crypto, no leverage, mandatory human approval, hard position-size caps) but **it is not financial advice, and trading involves risk of loss.** Read [SKILL.md](SKILL.md) in full before connecting it to any account of your own.

---

## 1. Setup — connecting Robinhood's Agentic Trading AI

This agent runs as a [Claude Code Skill](https://docs.claude.com/en/docs/claude-code) and talks to your account through **Robinhood's Agentic Trading MCP server** (currently in beta, US/desktop only).

1. **Enable Agentic Trading on Robinhood.** From the Robinhood app/desktop, opt in to agentic trading beta and fund a **dedicated cash account** — never reuse an existing margin/individual account. This SOP assumes a small, isolated account (e.g. ~$2,000) used for nothing else.
2. **Connect the MCP server** in Claude Code:
   ```bash
   claude mcp add robinhood-trading --transport http https://agent.robinhood.com/mcp/trading
   ```
3. **Authorize.** Auth is OAuth (no static token) — a browser window opens for the PKCE round-trip. Approve in-browser and paste the resulting `localhost/callback?code=...` URL back immediately; the flow expires fast if you wait.
4. **Confirm the correct account before ever placing a trade.** Call `get_accounts` and verify you're pointed at the account with `agentic_allowed: true`. Any other account (e.g. a default margin account) will show `agentic_allowed: false` and must never be traded.
5. **Keep Robinhood's own "review before action" gate ON.** This SOP layers its own human-approval workflow on top (see [SKILL.md](SKILL.md#approval-workflow---human-in-the-loop-then-autonomous)) — every proposal is presented and requires explicit approval before any order executes. Nothing here auto-trades out of the box.
6. **Clone this repo / drop it into your project** and point Claude Code at it. Start a session with the prompt at the bottom of [RESUME.md](RESUME.md) to pick up (or begin) the loop with full context: current positions, stops, and rules.

**Known platform quirk:** Robinhood has been observed blocking the *second* trade on a freshly agentic-enabled account until the account's investor-profile questionnaire is completed (`applink.robinhood.com/investment_profile?...`). If a second order 400s, complete that questionnaire in-app and retry.

---

## 2. What this does

The agent watches **four independent, publicly-disclosed signal sources** and only proposes a trade when they clear a strict qualifying bar. Any one signal can trigger a buy; a stock backed by multiple is the highest-conviction setup.

| Signal | What it clones | Gate |
|---|---|---|
| **🏛️ Politician clusters** | U.S. House/Senate members' disclosed stock buys | 2+ different members buying the same ticker within ~6 weeks, after dropping members with no real track record |
| **🏢 Insider clusters** | CEOs/CFOs/directors buying their own company's stock on the open market (SEC Form 4) | 2+ different insiders, genuine open-market purchases only (never grants, exercises, or 10b5-1 planned trades) |
| **💼 Superinvestor 13F clusters** | Top-performing fund managers tracked on [dataroma.com](https://www.dataroma.com) | 2+ distinct funds newly buying or materially adding to the same stock in the same 13F quarter |
| **📊 Prediction-market divergence** | Kalshi/Polymarket implied odds on macro prints (CPI, payrolls, FOMC) vs. independent anchors (Cleveland Fed nowcast, FRED) | Only fires on a clear divergence, expressed via long ETF rotation — never event contracts directly |

Every candidate that clears its signal-specific gate still has to pass a **shared safety gate**: long U.S. equities/ETFs only, ≥$5/share and ≥$2B market cap, no leverage, no OTC, not within 2 days of earnings, not already up >15% since the source trade, and sized small (~$35-50 per position, capped at 35% of the account).

Trades then go through a **human-in-the-loop approval workflow** — every proposal states which signal(s) fired, the exact people/funds/data behind it, and a one-sentence thesis — before anything is placed. Full rules, rationale, and edge-case rulings live in [SKILL.md](SKILL.md) and the [references/](references/) folder; live state and trade history are in [RESUME.md](RESUME.md) and [trade-log.md](trade-log.md).

---

## 3. Credits

The starter pack and original concept for this agent came from **Ryan Doser**.

📺 YouTube: [@RyanDoserAI](https://www.youtube.com/@RyanDoserAI)

All strategy rules, guardrails, and signal logic in this repo were then customized and extended on top of that starting point — see [SKILL.md](SKILL.md) for the full, current SOP.

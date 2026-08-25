# Arena Finance Skills

A skill library for finance, quant and trading **research** in Arena.

It is **not** a trading bot. Nothing here runs on its own, connects to a broker, holds a
wallet or places an order. It is a body of method — how to frame a question, what to check,
what invalidates a conclusion — that Arena loads automatically when you ask a finance
question.

---

## How to start a session

Say this once at the start of a chat:

> **Read AGENTS.md and automatically choose the relevant skills for this task.**

Then describe what you want in plain language. That is the whole setup.

**You never pick a profile.** There are none. Arena reads
[`skills/INDEX.md`](skills/INDEX.md), classifies your request and loads the three to six
skills that fit. You do not need to know what is in the library.

## What it covers

| Area | Examples |
|---|---|
| Finance & equity research | Single stocks, ETFs, options, macro, rates, FX, credit, commodities, events |
| Quant methodology | Signal design, regime detection, volatility, correlation, cointegration |
| Backtesting | Design, walk-forward validation, cost realism, result auditing |
| Portfolio & risk | Exposure, concentration, drawdown, VaR, sizing as analysis |
| Strategy work | Thesis validation, evidence gaps, strategy specification and critique |
| TradingView | Pine Script v6 generation, Strategy Tester validation, repaint audits |
| Market data | London Strategic Edge catalogue, candles, fundamentals, options flow, browser backtester |
| Crypto / DeFi | Read-only on-chain, pool, tokenomics and liquidity research |
| Trade process | Pre-trade checks, trade planning, journaling, post-trade review |

## Example prompts

**Equity research**
> Build me a research brief on Airbus: business drivers, what the last results changed, and the three things that would break the bull case.

**Strategy design**
> I want to buy the S&P when it closes above its 200-day average and volatility is falling. Pull that apart — what's wrong with it, and what would I need to test before believing it?

**Pine Script generation**
> Write a Pine v6 strategy: long when the 20 EMA crosses above the 50 EMA on the 4h, 2 % stop, 4 % target, 0.05 % commission per side. Include a date filter and tell me where it could repaint.

**TradingView validation**
> My Strategy Tester shows a 2.4 Sharpe over 18 months on 41 trades. Audit it before I get excited.

**London Strategic Edge data planning**
> I want five years of hourly EURUSD plus the US economic calendar for the same period. What exactly do I request, how many rows is that, and what will bite me?

**Portfolio risk**
> Here are my 14 positions with weights. Where is the hidden concentration, and what's my real single-factor exposure?

**Trade journal review**
> Here are my last 60 trades. Find the recurring mistake — not the losing trades, the pattern behind them.

## What it will not do

By design, and not negotiable at runtime:

- ❌ no broker or exchange account connection
- ❌ no order placement, modification or cancellation
- ❌ no wallet, seed phrase, private key or transaction signing
- ❌ no DEX swaps, bundle submission or copy trading
- ❌ no TradingView alerts routed to an execution venue
- ❌ no automated live execution of any kind

Material providing those capabilities was **deliberately not imported** — omitted, not
disabled, so it cannot be read by accident. Details in
[`SOURCES.lock.md`](SOURCES.lock.md) and [`SECURITY.md`](SECURITY.md).

Position sizing, exit rules and execution planning are supported **as analysis** — numbers
and checklists you act on yourself.

Two more things it will not do: **invent market data**, and **invent backtest results**. If a
number is not sourced, Arena is instructed to say so rather than produce something
plausible.

## Layout

```text
AGENTS.md                  Root policy and automatic router — read first
SECURITY.md                Secrets, scope limits, untrusted third-party content
SOURCES.lock.md            What was imported, what was omitted, and why
skills/
  INDEX.md                 Routing table — the map Arena reads
  orchestration/           Task classification and evidence discipline
  finance-research/        Category router → equities, macro, rates, credit…
  portfolio-risk/          Category router → analytics, concentration, risk metrics
  trading-process/         Regime, catalysts, thesis, sizing, journaling  (marian2js)
  quantitative/            Backtesting and risk metrics  (wshobson, agent-skills-hub)
  llmquant/                Broad finance research categories  (LLMQuant)
  crypto-research/         Read-only crypto, DeFi and quant methods  (agiprolabs)
  london-strategic-edge/   Market data + browser backtester workflows
  pine-script/             Pine Script v6 generation
  tradingview-validation/  Strategy Tester auditing
third-party-licenses/      Upstream licences (all MIT)
pine/                      Your generated Pine scripts
reports/                   Your validation and research reports
```

## Credentials

None are needed to use this library, and Arena is instructed never to ask for any. If you
later enable authenticated market-data access, the key belongs in a **secure runtime
secret** — never in this repository, a prompt, a Pine script or a report. See
[`SECURITY.md`](SECURITY.md).

## Sources

All imported material is MIT-licensed; licences are archived in
[`third-party-licenses/`](third-party-licenses/) and every source, commit and omission is
recorded in [`SOURCES.lock.md`](SOURCES.lock.md).

[marian2js/trading-skills](https://github.com/marian2js/trading-skills) ·
[wshobson/agents](https://github.com/wshobson/agents) ·
[agent-skills-hub](https://github.com/agent-skills-hub/agent-skills-hub) ·
[LLMQuant/skills](https://github.com/LLMQuant/skills) ·
[agiprolabs/claude-trading-skills](https://github.com/agiprolabs/claude-trading-skills)

---

*Research material only. Nothing here is investment advice.*

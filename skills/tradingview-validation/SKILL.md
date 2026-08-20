---
name: tradingview-validation
description: Validation protocol for turning strategy rules into Pine v6 and checking a TradingView Strategy Tester result against another research or backtest source. Use when comparing signals, diagnosing discrepancies, or auditing a backtest before trusting it.
category: validation
read_only: true
---

# TradingView validation

Two jobs: turn stated rules into faithful Pine v6, and decide whether a Strategy Tester
result can be believed. Pairs with [`../pine-script/SKILL.md`](../pine-script/SKILL.md) (how
to write the code) and, when the comparison source is LSE,
[`../london-strategic-edge/SKILL.md`](../london-strategic-edge/SKILL.md).

The default posture is **sceptical**. Most surprising backtest results are artefacts.

---

## Phase 1 — Rules to Pine v6

Never translate a vague description. Force the rules to be explicit first; ambiguity here
becomes an unexplainable discrepancy later.

**Specification checklist — get an answer or state an assumption for each:**

| Item | Why it matters |
|---|---|
| Instrument + exchange | `AAPL` on which venue; `EURUSD` spot, CFD or future |
| Timeframe, and any higher timeframe used | Bar construction differs by provider |
| Entry condition, exactly | "Crosses above" on close, or intrabar? |
| Confirmation | Signal bar close, or next bar open? |
| Exit: stop, target, time-based, opposite signal | All of them, with priority |
| Position sizing | Fixed qty, % equity, or risk-based |
| Costs | Commission per side, spread, slippage |
| Session and date window | RTH only? Which timezone? |
| Pyramiding / max concurrent positions | Default is one |
| What data adjustment is assumed | Split- and/or dividend-adjusted |

Then write the code per the Pine skill. Restate the rules back in one short list beside the
code so the user can confirm the translation before reading any result.

## Phase 2 — Syntax and compile review

Pine cannot be compiled here; verify conceptually against the
[official v6 docs](https://www.tradingview.com/pine-script-docs/):

- `//@version=6` first line; no v4/v5 idioms (`study()`, `security()` bare form, old `input()`).
- Type discipline: series vs simple vs const, especially in `request.security()` arguments,
  `input.*` defaults and `strategy()` parameters.
- `strategy.*` calls only in a `strategy()` script; `indicator()` scripts plot only.
- All variables assigned before use across branches; `var`/`varip` used deliberately.
- No `[-1]`, no future reference.
- Ask the user to paste the compiler error if there is one — do not guess at a red squiggle.

## Phase 3 — Assumption comparison

Before comparing numbers, compare **engines**. Build this table and fill both columns; each
mismatched row is a candidate explanation.

| Dimension | TradingView Strategy Tester | Other source (LSE, Python, broker) |
|---|---|---|
| Symbol / ticker id | | |
| Exchange / venue | | |
| Data provider and feed | | |
| Price adjustment (split, dividend) | | |
| Session (RTH / extended) | | |
| Timezone of bar timestamps | | |
| Bar construction (open alignment, 4h/weekly anchoring) | | |
| Bar timestamp convention (open vs close) | | |
| Commission model | | |
| Spread treatment | | |
| Slippage model | | |
| Fill assumption (close vs next open) | | |
| Intrabar stop/target resolution | | |
| Initial capital and currency | | |
| Position sizing rule | | |
| History start date | | |
| Handling of gaps, halts, holidays | | |

Rule of thumb: if two engines disagree, the cause is in this table roughly nine times out of
ten — not in the strategy logic.

## Phase 4 — Repainting and look-ahead audit

Run every item; report pass/fail, not a summary judgement.

1. **Unconfirmed bars** — does the signal use `barstate.isconfirmed`? If not, live behaviour
   will differ from history.
2. **`request.security()`** — is every call `lookahead_off`? Does it reference `close[1]`
   rather than a forming HTF value?
3. **`calc_on_every_tick`** — `false` for reproducible backtests.
4. **Pivots / centred windows** — any construct needing future bars to confirm repaints by
   construction. Document the confirmation lag.
5. **Full-sample statistics** — a z-score using the whole series' mean or standard deviation
   is look-ahead. It must be rolling.
6. **`process_orders_on_close`** — changes fill timing; make sure it matches the stated rule.
7. **Bar replay test** — ask the user to run TradingView's bar replay over a known window and
   confirm the signals match the historical plot. This is the cheapest empirical repaint
   check, and it is worth insisting on.
8. **Live-vs-history** — after a few live bars, do the plotted historical signals still sit
   where they did?

## Phase 5 — Signal comparison

Do not compare summary statistics first — compare **individual signals**.

1. Pick a window with 20–50 signals (not the whole history).
2. Export or list signals from both sides: timestamp, direction, price.
3. Align on bar timestamp, normalising timezone **before** matching.
4. Classify every difference:

| Class | Typical cause |
|---|---|
| Present in one source only | Data gap, session filter, symbol mismatch |
| Same bar, different price | Fill assumption, spread, adjustment |
| Off by exactly one bar | Timezone, bar-close convention, confirmation lag |
| Off by a variable number of bars | Different bar construction or HTF handling |
| Same signals, different P&L | Costs, sizing, exit resolution |

An off-by-one-bar pattern across the board is almost always timezone or confirmation, not
logic. Say so rather than rewriting the strategy.

5. Quantify: match rate, count by class, and the P&L impact of each class where computable.

## Phase 6 — Result plausibility audit

Before any result is discussed as if it were meaningful:

| Check | Red flag |
|---|---|
| Trade count | Fewer than ~30 — statistically meaningless |
| P&L concentration | Top 3 trades produce most of the profit |
| Period coverage | Only one regime (e.g. 2020–2021 only) |
| Costs | Zero or unrealistically low |
| Win rate vs payoff | 90 % win rate with a huge tail loss = short-volatility profile |
| Drawdown duration | Long time under water is often unlivable even if final P&L is good |
| Parameter sensitivity | Result collapses when a length changes by ±10 % — overfit |
| In-sample tuning | Parameters chosen on this same window |
| Survivorship | Delisted names absent from the universe |
| Liquidity | Simulated size exceeds a plausible fraction of bar volume |
| Short availability | Shorting assumed without borrow cost or availability |

For a genuine assessment, route to
[`../quantitative/wshobson/backtesting-frameworks/`](../quantitative/wshobson/backtesting-frameworks/)
and
[`../crypto-research/agipro/walk-forward-validation/`](../crypto-research/agipro/walk-forward-validation/).

## Phase 7 — Validation report

Write a concise report — under one page — and offer to save it in
[`../../reports/`](../../reports/). Structure:

```markdown
# Validation report — <strategy name>
Date: <YYYY-MM-DD>   Symbol: <exact ticker id>   Timeframe: <tf>
Sources compared: TradingView Strategy Tester vs <other source, with as-of date>

## 1. Rules as implemented
<numbered list, exactly as coded>

## 2. Assumption differences
<the Phase 3 table, mismatched rows only>

## 3. Repainting / look-ahead
<checklist result, pass/fail per item>

## 4. Signal comparison
Window: <dates>   Signals: TV <n> / Other <n>   Match rate: <%>
<discrepancy table by class, with suspected cause>

## 5. Result plausibility
<red flags found, and their likely magnitude>

## 6. Verdict
Reproducible: yes / partially / no
Explained discrepancies: <list>
Unexplained discrepancies: <list — these block any conclusion>
Confidence in the result: low / medium / high, and why

## 7. Next steps
<what would raise confidence: out-of-sample window, walk-forward, cost sensitivity>
```

## Non-negotiables

- **Never claim a strategy is profitable** without real results supplied by the user or
  retrieved through an allowed tool. No invented Sharpe, win rate, drawdown or equity curve —
  ever, not even as an illustration, unless labelled `ILLUSTRATIVE — NOT A RESULT` on the
  same line.
- **A validated strategy is not a profitable strategy.** Validation says the backtest
  measures what it claims to measure. It says nothing about the future.
- **Unexplained discrepancies block the conclusion.** "Close enough" is not a verdict; report
  them as open.
- No broker, no order routing, no execution webhook. Alerts are research-only.

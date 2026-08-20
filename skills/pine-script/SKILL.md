---
name: pine-script
description: Write, review and correct TradingView Pine Script v6 indicators and strategies that are Strategy Tester compatible, cost-aware and free of repainting or look-ahead bias. Use for any request to generate, debug or explain Pine code.
category: pine
source: https://www.tradingview.com/pine-script-docs/
read_only: true
---

# Pine Script v6 — generation and review

Produce Pine Script **v6** that compiles, backtests honestly and states its own assumptions.
The official [Pine Script v6 documentation](https://www.tradingview.com/pine-script-docs/) is
the authority; when this file and the docs disagree, the docs win — say so and check.

---

## 0. Boundaries (state these to the user when relevant)

- Pine **cannot** call an external REST API. It cannot reach the authenticated London
  Strategic Edge API, or any other HTTP endpoint. Pine sees only TradingView's own data via
  `request.*` functions.
- Pine **cannot place orders on an exchange**. `strategy.entry()` and friends drive the
  Strategy Tester simulator, nothing else.
- TradingView alerts may be created **for research and notification only**. Never route an
  alert webhook to a broker, an exchange or any execution service, and never put a
  credential in an alert message.
- A Strategy Tester result is a **simulation on one dataset**. It is not evidence of future
  profitability. Never describe a backtest as proof the strategy works.

---

## 1. Indicator or strategy?

| Use | When | Key point |
|---|---|---|
| `indicator()` | Visualising a signal, no P&L needed | Cannot use `strategy.*`; lighter, allows `overlay`, multiple plots |
| `strategy()` | You need entries, exits, equity curve, metrics | Runs the Strategy Tester; must declare costs |

Do not wrap a strategy in an indicator to "see the signals" — build the strategy and plot its
conditions. Do not add `strategy.*` calls to an indicator; it will not compile.

## 2. Skeleton for a Strategy Tester-compatible strategy

```pinescript
//@version=6
// ASSUMPTIONS (edit before trusting any result):
//  - Costs below are placeholders. Replace with YOUR broker's real commission and spread.
//  - Signals evaluate on CONFIRMED bars only (see useConfirmed).
//  - No look-ahead: higher-timeframe data requested with lookahead_off.
//  - Backtest results are a simulation on one dataset, not evidence of future returns.
strategy(
     title             = "Example strategy",
     overlay           = true,
     initial_capital   = 10000,
     default_qty_type  = strategy.percent_of_equity,
     default_qty_value = 10,              // percent of equity per trade
     commission_type   = strategy.commission.percent,
     commission_value  = 0.05,            // % per side — set to your real cost
     slippage          = 2,               // in ticks — set to your real cost
     currency          = currency.NONE,
     calc_on_every_tick        = false,   // bar-close evaluation
     process_orders_on_close   = false,   // fills at next bar open
     pyramiding        = 0)

// ── Inputs ────────────────────────────────────────────────────────────────
grpSig  = "Signal"
grpRisk = "Risk"
grpTime = "Date & session filter"

fastLen = input.int(20,  "Fast EMA length", minval = 1, group = grpSig)
slowLen = input.int(50,  "Slow EMA length", minval = 2, group = grpSig)
useConfirmed = input.bool(true, "Act on confirmed bars only", group = grpSig,
     tooltip = "Off = intrabar signals that can repaint.")

stopPct   = input.float(2.0, "Stop loss %",   minval = 0.1, step = 0.1, group = grpRisk)
targetPct = input.float(4.0, "Take profit %", minval = 0.1, step = 0.1, group = grpRisk)
riskPct   = input.float(1.0, "Risk per trade % of equity", minval = 0.1, step = 0.1, group = grpRisk)

startDate = input.time(timestamp("01 Jan 2015 00:00 +0000"), "Backtest start", group = grpTime)
endDate   = input.time(timestamp("01 Jan 2030 00:00 +0000"), "Backtest end",   group = grpTime)
useSession   = input.bool(false, "Restrict to session", group = grpTime)
sessionInput = input.session("0930-1600", "Session", group = grpTime)

// ── Filters ───────────────────────────────────────────────────────────────
inDateRange = time >= startDate and time <= endDate
inSession   = not useSession or not na(time(timeframe.period, sessionInput))
tradingOK   = inDateRange and inSession

// ── Signal logic ──────────────────────────────────────────────────────────
fast = ta.ema(close, fastLen)
slow = ta.ema(close, slowLen)

rawLong  = ta.crossover(fast, slow)
rawShort = ta.crossunder(fast, slow)

// barstate.isconfirmed prevents acting on a still-forming bar.
longSignal  = rawLong  and (not useConfirmed or barstate.isconfirmed)
shortSignal = rawShort and (not useConfirmed or barstate.isconfirmed)

// ── Position sizing (simulation only — no broker access) ──────────────────
// Risk-based size: (equity * risk%) / (entry - stop distance)
stopDist = close * stopPct / 100
qty = stopDist > 0 ? (strategy.equity * riskPct / 100) / stopDist : na

// ── Orders (Strategy Tester simulation) ───────────────────────────────────
if tradingOK and longSignal and not na(qty)
    strategy.entry("Long", strategy.long, qty = qty)

if tradingOK and shortSignal
    strategy.close("Long", comment = "Exit on cross")

longStop   = strategy.position_avg_price * (1 - stopPct   / 100)
longTarget = strategy.position_avg_price * (1 + targetPct / 100)

if strategy.position_size > 0
    strategy.exit("Long exit", from_entry = "Long", stop = longStop, limit = longTarget)

// ── Plots and diagnostics ─────────────────────────────────────────────────
plot(fast, "Fast EMA", color = color.new(color.teal,   0))
plot(slow, "Slow EMA", color = color.new(color.orange, 0))
plotshape(tradingOK and longSignal,  title = "Long",  style = shape.triangleup,
     location = location.belowbar, color = color.new(color.teal, 0), size = size.tiny)
plotshape(tradingOK and shortSignal, title = "Short", style = shape.triangledown,
     location = location.abovebar, color = color.new(color.red, 0),  size = size.tiny)
bgcolor(tradingOK ? na : color.new(color.gray, 90), title = "Outside test window")

// ── Alerts (research/notification only — never wire to a broker) ──────────
alertcondition(longSignal,  title = "Long signal",  message = "Long signal on {{ticker}}")
alertcondition(shortSignal, title = "Short signal", message = "Short signal on {{ticker}}")

if longSignal
    alert('{"event":"long_signal","ticker":"' + syminfo.tickerid +
          '","tf":"' + timeframe.period + '","close":' + str.tostring(close) +
          ',"time":' + str.tostring(time) + '}', alert.freq_once_per_bar_close)
```

## 3. Costs — never leave them at zero

A strategy that is profitable at zero cost and unprofitable at realistic cost is
unprofitable. Always declare, and always tell the user the values are placeholders:

- **Commission** — `commission_type` + `commission_value`, per side.
- **Slippage** — `slippage` in **ticks**; scale it to the instrument's liquidity and your
  timeframe. Intraday and illiquid names need more, not less.
- **Spread** — not a `strategy()` parameter. Either fold it into slippage or model it
  explicitly; state which you did.
- **Funding/borrow** — Pine does not model overnight financing. If the strategy holds
  positions, say the result omits it.

## 4. Date range and session filters

Always expose a backtest window (`input.time`) and gate orders on it. Without it, results
silently change as history extends and the user cannot reproduce them.

For intraday work expose a session filter and be explicit about timezone: `time(timeframe.period,
sessionInput)` returns `na` outside the session. Session boundaries and DST are a frequent
source of TradingView-vs-other-engine mismatches.

## 5. Repainting — the main correctness risk

A script repaints when the historical picture differs from what was knowable in real time.

| Cause | Fix |
|---|---|
| Acting on an unconfirmed bar | Gate on `barstate.isconfirmed`, or accept and document it |
| `request.security()` with `lookahead_on` | Use `barmerge.lookahead_off` |
| Referencing a higher-timeframe value before it closes | Offset by one closed HTF bar |
| `calc_on_every_tick = true` | Set `false` for backtest reproducibility |
| Historical vs realtime `[1]` differences | Test on a replayed and a live bar; compare |
| Redrawing objects on past bars | Acceptable for annotation, never for signals |

Safe higher-timeframe pattern:

```pinescript
// Confirmed higher-timeframe close, no look-ahead.
htfClose = request.security(syminfo.tickerid, "D", close[1],
     lookahead = barmerge.lookahead_off)
```

Requesting `close` with `lookahead_off` on a still-forming HTF bar still leaks a forming
value into the current bar; `close[1]` returns the **last fully closed** HTF bar. Use it
unless you deliberately want the forming value and have documented that choice.

## 6. Look-ahead bias beyond `request.security`

- Never use a future bar: `close[-1]` is not available and any construct implying it is wrong.
- Do not use `ta.highest(high, n)` centred on the current bar as a signal — it is
  backward-looking by construction, but check any custom pivot logic, which typically needs
  `n` bars **after** the pivot to confirm and therefore repaints.
- Do not compute an indicator on the whole series and then reference it as if it were
  available early (e.g. a normalisation using the full-sample mean or standard deviation).
- Beware `strategy.exit()` when a bar contains both stop and target: TradingView resolves it
  with an assumption. Use `strategy.risk` guards or a lower timeframe to disambiguate, and
  state the ambiguity.

## 7. Position sizing without broker access

Sizing here is simulation input, not a broker instruction. Two usable modes:

- **Fixed fractional** — `default_qty_type = strategy.percent_of_equity`, simple and stable.
- **Risk-based** — size so that hitting the stop costs a fixed % of equity, as in the
  skeleton. Guard against `stopDist <= 0` and against sizes below the instrument's minimum.

Always say that real fills, minimum lot sizes, margin and instrument specifications are not
represented.

## 8. Plots, diagnostics and comments

- Plot the indicator values that drive the signal so the user can verify visually.
- Mark entries/exits with `plotshape`; shade the excluded date window.
- Add a `table` or `label` with key state (position size, stop, target) when debugging.
- Comment **assumptions and limitations**, not syntax. A comment saying
  `// commission is a placeholder` is worth more than `// calculate EMA`.

## 9. Alerts

`alertcondition()` for simple notifications; `alert()` for dynamic messages including a JSON
payload for research logging. Rules:

- Prefer `alert.freq_once_per_bar_close` — `once_per_bar` can fire on an unconfirmed bar.
- JSON payloads are for the user's own research log. **No credential, no token, no broker
  webhook.**
- An alert firing is not a trade. Say so.

## 10. Review checklist before delivering code

1. `//@version=6` on the first line; v6 syntax throughout (no `study()`, no v4/v5 idioms).
2. Compiles conceptually: types consistent, no series/simple mismatch, all `input.*` before
   use, `strategy.*` only inside `strategy()` scripts.
3. Costs declared and flagged as placeholders.
4. Date range and (if intraday) session filter present and gating orders.
5. `barstate.isconfirmed` handling explicit.
6. Every `request.security()` uses `lookahead_off`, and `[1]` where a closed value is meant.
7. No future reference anywhere.
8. Exits defined — stop **and** target — plus the intrabar-ambiguity note.
9. Plots let the user verify signals visually.
10. Header comment lists assumptions and limitations.
11. Alerts, if present, carry no secret and no execution route.
12. The reply states that no result exists until the user runs the Strategy Tester, and
    points to [`../tradingview-validation/SKILL.md`](../tradingview-validation/SKILL.md).

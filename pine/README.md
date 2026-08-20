# pine/

Generated TradingView Pine Script v6 files live here.

## Conventions

- One strategy or indicator per file, named `<concept>-<timeframe>.pine`
  (e.g. `ema-cross-4h.pine`, `atr-breakout-1d.pine`).
- Every file starts with `//@version=6` and a header comment listing **assumptions and
  limitations** — cost placeholders, confirmation behaviour, known repaint risk.
- Keep files small and text-only. No data exports, no chart images.

## Rules

- **No credentials.** No API key, token or webhook secret in a script or in an alert message.
- **No execution routing.** Alerts generated from these scripts are for research and
  notification only. Never point an alert webhook at a broker, an exchange or any endpoint
  that can place a trade.
- **Pine cannot call external APIs**, including the London Strategic Edge REST API. Pine sees
  only TradingView data.
- A Strategy Tester result is a simulation on one dataset — not evidence of future
  profitability.

Method: [`../skills/pine-script/SKILL.md`](../skills/pine-script/SKILL.md).
Before trusting a result: [`../skills/tradingview-validation/SKILL.md`](../skills/tradingview-validation/SKILL.md).

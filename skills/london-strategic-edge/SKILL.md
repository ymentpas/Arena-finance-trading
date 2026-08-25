---
name: london-strategic-edge
description: Read-only research workflows for London Strategic Edge market data (catalog, candles, macro and bond series, economic calendar, insider trades, dividends, splits, fundamentals, options chains, flow and greeks, export jobs) and the free browser backtester. Use when planning data acquisition, interpreting an LSE backtest report, or reconciling LSE data with TradingView.
category: market-data
source: https://londonstrategicedge.com/
read_only: true
dependency: LSE API key required for API access — never requested or stored here
---

# London Strategic Edge — market data and browser backtesting

Research-only skill for the London Strategic Edge (LSE) platform: what data exists, how to
plan a pull, how to read the browser backtester's report, and how to reconcile it with
TradingView.

Based on the official documentation:
[site](https://londonstrategicedge.com/) ·
[API documentation](https://londonstrategicedge.com/api-documentation/) ·
[free market data API](https://londonstrategicedge.com/free-market-data-api/) ·
[free backtesting](https://londonstrategicedge.com/backtesting-free/) ·
[lse-data client](https://github.com/londonstrategicedge/lse-data).
Documentation consulted 2026-08-21; **verify against the live docs before relying on a
specific limit or field** — the provider updates them.

---

## 0. Non-negotiable rules

**Credentials**

- No API key belongs in GitHub, in Markdown, in Pine Script, in a prompt, in a log or in a
  report under `reports/`.
- **Never ask the user for their key**, and never ask for it during installation of this
  library.
- If authenticated access is enabled later, code and tools must read the key from a **secure
  runtime secret** — for example the environment variable `LSE_API_KEY` — never from a file
  in this repository. Keys look like `lse_live_…`; if one ever appears in the repo, treat it
  as compromised and rotate it (see [`../../SECURITY.md`](../../SECURITY.md)).

**Scope**

- The documented public API is a **market-data API**. It reads data. It places no orders and
  connects to no broker. Do not invent undocumented endpoints — in particular there is **no
  documented backtest-job API**; the backtester is a browser product.
- `/catalog` and `/meta` are the source of truth for what exists. Anything not listed there
  is not part of the public surface. If you are unsure a field exists, say so rather than
  guessing.
- Do not scrape or automate the website UI without explicit permission from the provider.
- Do not redistribute downloaded data contrary to the provider's terms. Keep derived
  analysis, not raw dumps — and never commit raw data to this repository.
- **Do not build a Python integration in this repository.** The official `lse-data` client
  exists and is documented below as reference; installing it and wiring a pipeline is out of
  scope here.

---

## 1. Capability map

| Need | Surface | Notes |
|---|---|---|
| What instruments/series exist | `GET /catalog` | ~22,000 rows: `dataset`, `symbol`, `name`, `ticks`, `first_tick`, `last_tick`, `years`; series rows add `last_value`, `unit`, `category`, `frequency`, `country` |
| Product shape, timeframes, access map | `GET /meta` | Says which endpoint reads which dataset |
| Reference dataset row counts and spans | `GET /reference` | |
| OHLCV candles | `GET /candles` | `symbol`, `timeframe`, `start`, `end`, `order`, `limit`, optional `dataset` |
| Macro series and bond yields | `GET /series` | Class resolves from the catalog; `dataset` pin optional (`economics`, `bonds`) |
| Economic calendar | `GET /ref/economic_calendar` | Filters: `region`, `event` (substring), `released=1` |
| Insider trades | `GET /ref/insider_trades` | Filters: `symbol`, `type` (SEC code, e.g. `P-Purchase`) |
| Dividends / splits | `GET /ref/dividends`, `GET /ref/stock_splits` | Date column `effective_date` |
| Financial reports | `GET /ref/financial_reports` | `report_type` = `income`/`balance`/`cashflow`, `period` = `FY`/`Q1`… |
| Fundamentals / profiles | `GET /ref/stock_fundamentals`, `GET /ref/company_profiles` | No date column |
| Futures positioning | `GET /ref/cot` | `symbol` = futures market code |
| Bond yields as reference | `GET /ref/bond_yields` | `symbol` = tenor code, e.g. `US10Y` |
| Options chain | `GET /options/chain` | `underlying`, `type`, `expiry`, `strike`/`strike_min`/`strike_max`, `min_dte`/`max_dte` |
| Options flow (prints) | `GET /options/flow` | `min_premium`, `type`, `expiry`, `max_dte`, `start`, `end`; defaults `order=desc` |
| Per-contract candles | `GET /options/candles` | `ticker` = OSI, 1-minute premium OHLC + averaged greeks |
| Bulk pulls / raw ticks | `POST /export` → `GET /export/{id}` → `/download` | Parquet or Arrow; ticks **only** this way |
| Allowance and limits | `GET /usage` | Free, outside the per-minute limit |

Base URL `https://api.londonstrategicedge.com/vault`, one header: `x-api-key`.

**Timeframes (14):** `1s 5s 15s 30s 1m 3m 5m 15m 30m 1h 4h 1d 1w 1mo` (candles default `1m`).
Bond-yield series accept `1d` (default) or `1h`.

**Coverage and depth (per the docs, verify before quoting):** US stocks from 2003, forex from
2009, crypto from 2017, options prints from 2014, per-contract minute bars from January 2026;
thousands of macro series across many countries, bond-yield tenors back to 1990. Stock and
ETF candles are **split adjusted**. Forex candles carry **no volume** field — FX has no
consolidated tape.

**Options ticker format (OSI):** root + expiry `YYMMDD` + `C`/`P` + strike × 1000 zero-padded
to 8 digits. The AAPL 300 call expiring 2026-06-12 is `AAPL260612C00300000`.

---

## 2. Limits, errors and missing data

- One page per query call, currently **5,000 rows**; longer ranges page or become an export.
- Response bytes count against a **monthly allowance shared with the WebSocket**; each
  response carries `X-Data-Bytes`. Discovery calls (`/catalog`, `/meta`, `/reference`) and
  `/usage` are free and outside the per-minute limit.
- Exports have a **separate hourly cap**; artefacts live **48 hours**; downloads resume with
  a `Range` header. `-1` in `/usage` means unlimited.
- Export job statuses: `queued`, `running`, `ready`, `failed`, `expired`.

| Status | Meaning | Correct reaction |
|---|---|---|
| 400 | Bad parameter (unknown timeframe, malformed date) | Fix the request; check `/meta` |
| 401 | Missing/invalid key | Do **not** ask the user to paste it — tell them to check their runtime secret |
| 403 | Key inactive or expired | Same; the user resolves it on the provider side |
| 404 | Unknown dataset/job, or symbol with no data | Re-check the exact catalog symbol |
| 409 | Download before job ready | Poll `/export/{id}` first |
| 410 | Export expired (>48 h) | Re-submit the job |
| 429 | Rate limit or monthly allowance reached | Back off; narrow the window; check `/usage` |
| 503 | Export storage briefly full | Retry later |

**Missing data is a finding, not a nuisance.** A gap, a halted symbol, a delisting, a zero-volume
bar or a stale macro print changes a backtest. Report gaps explicitly; never interpolate
silently, and never fill a hole with a plausible number.

---

## 3. Provenance discipline

Every figure sourced from LSE must carry, in the answer or the report:

- **dataset and symbol exactly as they appear in `/catalog`** (`BTC/USD`, not "bitcoin");
- **timeframe** and the **window** actually returned (first and last bar timestamps);
- **as-of timestamp**, and the fact that stock/ETF candles are split-adjusted;
- **row count** returned and whether the page cap truncated it;
- known gaps or stale series.

Symbol mapping is the classic silent error: the same underlying differs between LSE, the
user's broker and TradingView (exchange suffixes, `EUR/USD` vs `EURUSD`, adjusted vs
unadjusted, spot vs future vs CFD). Resolve it against `/catalog` and state the mapping.

---

## 4. Planning a data pull (the normal workflow)

1. **Define the question first** — instrument, resolution, window, and what would change the
   conclusion. Do not pull first and think later.
2. **Discover**: `/catalog` for the exact symbol and its real history span; `/meta` for which
   endpoint and which timeframes apply.
3. **Estimate size**: rows ≈ window ÷ timeframe. Above 5,000 rows, plan pagination or an
   export. Check `/usage` before a large pull.
4. **Choose the surface**: interactive JSON for exploration, export (Parquet/Arrow) for bulk
   or raw ticks.
5. **Pull the smallest sufficient slice.** A regime study rarely needs 1-second bars.
6. **Record provenance** as in §3.
7. **Keep derived results only** — summary tables and reports in `reports/`. Raw data stays
   out of this repository (see [`../../AGENTS.md`](../../AGENTS.md) §9).

**Reference only — the official Python client.** `pip install lse-data`, then
`client.candles("BTC/USD", "1d", start="2026-01-01")`, `client.economics("cpi_yoy")`,
`client.options("AAPL", type="call", max_dte=30)`, `client.history("AAPL", timeframe="1m",
start="2015-01-01")`. Shown so you can describe an approach to the user — **do not install it
or build a pipeline in this repository.**

---

## 5. Browser backtester workflow

LSE runs a free browser backtester (e.g. `/backtest/XAUUSD`) with no account required.

**Two modes**

- **Strategy builder** — form-driven: entry condition, exit condition, stop loss, take
  profit, from indicator conditions. No code.
- **Manual mode** — bar-by-bar replay recording the trades you would have taken; it measures
  discretion, not a rule set.

**Documented free-plan caps:** 50 backtest jobs/day, 10 stored reports. Same data store as
the live charts; daily history to 2003 on long-listed instruments, intraday from 1 minute.

**Reading the report — what it gives you**

Equity curve, maximum drawdown, Sharpe ratio, profit factor, win rate, and a full trade
table.

**How to interpret it honestly**

| Metric | Read it as | Trap |
|---|---|---|
| Equity curve | Shape and path, not the endpoint | A smooth curve on few trades is noise |
| Max drawdown | Deepest peak-to-trough carried | Says nothing about duration — check time under water |
| Sharpe | Return per unit of volatility | Meaningless on a small sample; sensitive to the period |
| Profit factor | Gross profit ÷ gross loss; >1.0 means wins paid for losses | Inflated by one outlier |
| Win rate | Frequency, not edge | High win rate + tiny wins + huge losses = negative expectancy |
| Trade table | **The most important output** | Sort by P&L: if the top 3 trades carry the curve, the edge is unproven |

Always check: number of trades (a few dozen proves nothing), period covered and which regimes
it contains, whether costs are modelled, whether the stop/take-profit could have been hit
intrabar in an order the bar data cannot resolve, and whether the rules were tuned on this
same window (in-sample fitting).

Manual-mode results additionally embed hindsight: the operator saw the chart context. Treat
them as training, not evidence.

---

## 6. Reconciling LSE with TradingView

When an LSE backtest and a TradingView Strategy Tester run disagree, the strategy logic is
usually **not** the first cause. Check in this order:

1. **Symbol and venue** — different exchange, different composite/consolidated tape,
   CFD vs spot vs future.
2. **Adjustment** — LSE stock/ETF candles are split-adjusted; check dividend adjustment and
   the TradingView chart's own setting.
3. **Session and timezone** — RTH vs extended hours, exchange timezone vs UTC, daily bar
   cut-off. This alone shifts signals by a bar.
4. **Bar construction** — bar open alignment, weekly/monthly anchoring, how the provider
   builds 4h bars, gaps and holidays.
5. **Costs** — commission, spread, slippage assumptions on each side. LSE's form-driven
   builder and Pine's `strategy()` parameters express them differently.
6. **Fill assumptions** — signal on close vs execution at next open; intrabar stop/limit
   ordering; both engines guess when a bar contains both stop and target.
7. **Data depth** — different start dates mean different regimes, not a different edge.

Then, and only then, compare the rules. Produce a discrepancy table: bar timestamp, LSE
signal, TradingView signal, suspected cause. See
[`../tradingview-validation/SKILL.md`](../tradingview-validation/SKILL.md) for the full
protocol.

Expect residual differences. The goal is **explained** differences, not identical numbers.

---

## 7. Boundaries

- Read-only research. **No broker, no order, no execution** — the LSE public API offers none.
- Pine Script **cannot** call this authenticated REST API (see
  [`../pine-script/SKILL.md`](../pine-script/SKILL.md)).
- Never present an example response from this file as retrieved data. Show the request shape
  and the documented schema; label anything illustrative.
- If the key is unavailable, the honest answer is a **data-acquisition plan**, not invented
  numbers.

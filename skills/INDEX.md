# skills/INDEX.md — routing table

Compact routing table for every skill in this library. Read this before selecting skills; do
not read every `SKILL.md` to decide. Full policy: [`../AGENTS.md`](../AGENTS.md).

**Legend**
`RO` = read-only (no execution, no order, no wallet) — **all skills here are read-only**.
`dep-required` = needs a connector unavailable in this environment; use the method, not the
data path. `★` = authoritative version when several skills overlap.

Licences: **MIT** for every imported source. See [`../SOURCES.lock.md`](../SOURCES.lock.md).

---

## Orchestration — start here

| Skill | Path | Trigger | Source | Deps |
|---|---|---|---|---|
| finance-research-orchestrator ★ | [`orchestration/finance-research-orchestrator/`](orchestration/finance-research-orchestrator/) | **Every** finance/quant/trading request — classify, select, enforce evidence rules | Authored | — |

## Category routers

| Skill | Path | Trigger | Source | Deps |
|---|---|---|---|---|
| finance-research-category | [`finance-research/SKILL.md`](finance-research/SKILL.md) | Equity, macro, rates, FX, credit, commodities, events research | Authored | — |
| portfolio-risk-category | [`portfolio-risk/SKILL.md`](portfolio-risk/SKILL.md) | Holdings review, exposure, drawdown, VaR, sizing as analysis | Authored | — |

## Market data and platform skills

| Skill | Path | Trigger | Source | Deps |
|---|---|---|---|---|
| london-strategic-edge ★ | [`london-strategic-edge/SKILL.md`](london-strategic-edge/SKILL.md) | LSE catalog, candles, macro/bond series, calendar, insider trades, dividends, splits, fundamentals, options chain/flow/greeks, exports, browser backtester | Authored from official docs | API key via runtime secret only — **never requested here** |
| pine-script ★ | [`pine-script/SKILL.md`](pine-script/SKILL.md) | Write/debug/explain TradingView Pine v6 indicators and strategies | Authored from official docs | TradingView account (user side) |
| tradingview-validation ★ | [`tradingview-validation/SKILL.md`](tradingview-validation/SKILL.md) | Rules→Pine fidelity, Strategy Tester audit, signal discrepancy diagnosis, repaint checks | Authored | — |

---

## Trading process — `skills/trading-process/marian/`

Source: [marian2js/trading-skills](https://github.com/marian2js/trading-skills) · MIT · RO ·
no dependencies. Analysis and planning only — no execution material was imported.

| Skill | Path | Trigger |
|---|---|---|
| market-regime-analysis ★ | [`trading-process/marian/market-context/market-regime-analysis/`](trading-process/marian/market-context/market-regime-analysis/) | Trend/volatility/breadth read before choosing tactics |
| macro-event-analysis | [`trading-process/marian/market-context/macro-event-analysis/`](trading-process/marian/market-context/macro-event-analysis/) | CPI, FOMC, payrolls — positioning into a print |
| earnings-preview | [`trading-process/marian/market-context/earnings-preview/`](trading-process/marian/market-context/earnings-preview/) | Preparing for a specific earnings date |
| watchlist-review | [`trading-process/marian/idea-discovery/watchlist-review/`](trading-process/marian/idea-discovery/watchlist-review/) | Triaging a list of candidates |
| catalyst-map | [`trading-process/marian/idea-discovery/catalyst-map/`](trading-process/marian/idea-discovery/catalyst-map/) | Mapping upcoming catalysts to a name |
| thesis-validation ★ | [`trading-process/marian/thesis-validation/thesis-validation/`](trading-process/marian/thesis-validation/thesis-validation/) | Stress-testing an investment/trade thesis |
| evidence-gap-check | [`trading-process/marian/thesis-validation/evidence-gap-check/`](trading-process/marian/thesis-validation/evidence-gap-check/) | "What am I missing?" — unexamined assumptions |
| risk-reward-sanity-check | [`trading-process/marian/trade-construction/risk-reward-sanity-check/`](trading-process/marian/trade-construction/risk-reward-sanity-check/) | Is the R:R real or wishful |
| position-sizing | [`trading-process/marian/trade-construction/position-sizing/`](trading-process/marian/trade-construction/position-sizing/) | Sizing **as analysis only** |
| portfolio-concentration ★ | [`trading-process/marian/trade-construction/portfolio-concentration/`](trading-process/marian/trade-construction/portfolio-concentration/) | Correlated bets, hidden single-factor exposure |
| execution-plan-check | [`trading-process/marian/trade-construction/execution-plan-check/`](trading-process/marian/trade-construction/execution-plan-check/) | Reviewing a plan **on paper** — places no orders |
| position-management | [`trading-process/marian/position-oversight/position-management/`](trading-process/marian/position-oversight/position-management/) | Managing an open position as analysis |
| post-trade-review | [`trading-process/marian/review-learning/post-trade-review/`](trading-process/marian/review-learning/post-trade-review/) | Single-trade post-mortem |
| journal-pattern-analyzer ★ | [`trading-process/marian/review-learning/journal-pattern-analyzer/`](trading-process/marian/review-learning/journal-pattern-analyzer/) | Recurring mistakes across a journal |
| **workflow** pre-trade-check | [`trading-process/marian/workflows/pre-trade-check/`](trading-process/marian/workflows/pre-trade-check/) | Multi-step gate before committing to a trade |
| **workflow** portfolio-risk-review | [`trading-process/marian/workflows/portfolio-risk-review/`](trading-process/marian/workflows/portfolio-risk-review/) | Full portfolio risk pass |
| **workflow** earnings-trade-prep | [`trading-process/marian/workflows/earnings-trade-prep/`](trading-process/marian/workflows/earnings-trade-prep/) | End-to-end earnings preparation |
| **workflow** post-trade-debrief | [`trading-process/marian/workflows/post-trade-debrief/`](trading-process/marian/workflows/post-trade-debrief/) | End-to-end debrief |

`trading-process/marian/_lib/calculations.py` is **reference material** (formulas referenced
by relative links from three skills). Do not install or run it as an application.

## Quantitative — `skills/quantitative/`

| Skill | Path | Trigger | Source | Notes |
|---|---|---|---|---|
| backtesting-frameworks ★ | [`quantitative/wshobson/backtesting-frameworks/`](quantitative/wshobson/backtesting-frameworks/) | Backtest design, bias avoidance, framework choice | wshobson/agents · MIT | Authoritative over agipro equivalents |
| risk-metrics-calculation ★ | [`quantitative/wshobson/risk-metrics-calculation/`](quantitative/wshobson/risk-metrics-calculation/) | Sharpe, Sortino, VaR, CVaR, drawdown, beta — definitions and pitfalls | wshobson/agents · MIT | Authoritative for metric definitions |
| quant-analyst-methodology | [`quantitative/wshobson/quant-analyst-methodology/`](quantitative/wshobson/quant-analyst-methodology/) | How to approach a quant problem end to end | wshobson/agents · MIT | Converted from `agents/quant-analyst.md` |
| risk-manager-methodology ★ | [`quantitative/wshobson/risk-manager-methodology/`](quantitative/wshobson/risk-manager-methodology/) | Risk framework, limits, exposure governance | wshobson/agents · MIT | Converted from `agents/risk-manager.md` |
| quant-analyst (overview) | [`quantitative/agent-skills-hub/quant-analyst/`](quantitative/agent-skills-hub/quant-analyst/) | Short orientation on the quant-analyst role | agent-skills-hub · MIT | Overview only; wshobson versions are authoritative |

## Finance research — `skills/llmquant/`

Source: [LLMQuant/skills](https://github.com/LLMQuant/skills) · MIT · RO ·
**all `dep-required`: written against the LLMQuant Data connector, unavailable here.**
Use their analytical structure; source numbers elsewhere. Never ask for LLMQuant credentials.

| Skill | Path | Trigger |
|---|---|---|
| llmquant-equities ★ | [`llmquant/llmquant-equities/`](llmquant/llmquant-equities/) | Stock analysis, comparison, research memo, sell discipline |
| llmquant-etfs | [`llmquant/llmquant-etfs/`](llmquant/llmquant-etfs/) | ETF selection and comparison |
| llmquant-options | [`llmquant/llmquant-options/`](llmquant/llmquant-options/) | Options structures, IV, positioning |
| llmquant-equity-derivatives | [`llmquant/llmquant-equity-derivatives/`](llmquant/llmquant-equity-derivatives/) | Equity derivative structures |
| llmquant-macro | [`llmquant/llmquant-macro/`](llmquant/llmquant-macro/) | Macro regime, policy, growth/inflation |
| llmquant-rates-fx | [`llmquant/llmquant-rates-fx/`](llmquant/llmquant-rates-fx/) | Curves, carry, currency analysis |
| llmquant-credit | [`llmquant/llmquant-credit/`](llmquant/llmquant-credit/) | Spreads, issuers, credit cycle |
| llmquant-commodities | [`llmquant/llmquant-commodities/`](llmquant/llmquant-commodities/) | Energy, metals, agricultural |
| llmquant-events | [`llmquant/llmquant-events/`](llmquant/llmquant-events/) | Earnings, M&A, corporate actions |
| llmquant-market-intelligence | [`llmquant/llmquant-market-intelligence/`](llmquant/llmquant-market-intelligence/) | News flow, narrative tracking |
| llmquant-investor-lenses | [`llmquant/llmquant-investor-lenses/`](llmquant/llmquant-investor-lenses/) | Munger/Buffett/Marks-style mental models |
| llmquant-portfolio ★ | [`llmquant/llmquant-portfolio/`](llmquant/llmquant-portfolio/) | Construction, allocation, rebalancing analysis |
| llmquant-portfolio-lab | [`llmquant/llmquant-portfolio-lab/`](llmquant/llmquant-portfolio-lab/) | Portfolio experimentation workflows |
| llmquant-risk | [`llmquant/llmquant-risk/`](llmquant/llmquant-risk/) | Risk framing for a memo (metrics: see wshobson ★) |
| llmquant-strategies | [`llmquant/llmquant-strategies/`](llmquant/llmquant-strategies/) | Strategy archetypes (long/short, multi-strat…) |
| llmquant-crypto | [`llmquant/llmquant-crypto/`](llmquant/llmquant-crypto/) | Crypto from a traditional-finance lens |
| llmquant-prediction-markets | [`llmquant/llmquant-prediction-markets/`](llmquant/llmquant-prediction-markets/) | Prediction-market research |
| llmquant-data | [`llmquant/llmquant-data/`](llmquant/llmquant-data/) | LLMQuant Data usage patterns — `dep-required`, reference only |

## Crypto, quant methods and DeFi — `skills/crypto-research/agipro/`

Source: [agiprolabs/claude-trading-skills](https://github.com/agiprolabs/claude-trading-skills)
· MIT · RO. **45 of 67 upstream skills imported**; 22 omitted (execution/keys/out of scope) —
see [`../SOURCES.lock.md`](../SOURCES.lock.md). Each folder carries `references/` and
`scripts/` as **reference material** — read the method, do not install anything.

### Data and signal processing
| Skill | Trigger |
|---|---|
| [`ohlcv-processing`](crypto-research/agipro/ohlcv-processing/) ★ | Cleaning, resampling, aligning OHLCV data |
| [`feature-engineering`](crypto-research/agipro/feature-engineering/) | Building predictive features |
| [`signal-classification`](crypto-research/agipro/signal-classification/) | Classifying and scoring signals |
| [`custom-indicators`](crypto-research/agipro/custom-indicators/) | Designing an indicator from scratch |
| [`pandas-ta`](crypto-research/agipro/pandas-ta/) / [`ta-lib`](crypto-research/agipro/ta-lib/) | Standard indicator libraries (reference) |
| [`trading-visualization`](crypto-research/agipro/trading-visualization/) | Charting analysis results |
| [`sentiment-analysis`](crypto-research/agipro/sentiment-analysis/) | Sentiment as a research input |

### Statistical methods
| Skill | Trigger |
|---|---|
| [`correlation-analysis`](crypto-research/agipro/correlation-analysis/) ★ | Correlation structure, diversification |
| [`cointegration-analysis`](crypto-research/agipro/cointegration-analysis/) ★ | Pairs and spread relationships |
| [`mean-reversion`](crypto-research/agipro/mean-reversion/) | Mean-reversion methodology |
| [`regime-detection`](crypto-research/agipro/regime-detection/) | Quantitative regime identification |
| [`volatility-modeling`](crypto-research/agipro/volatility-modeling/) ★ | GARCH-family, realised vol, forecasting |
| [`market-microstructure`](crypto-research/agipro/market-microstructure/) | Crypto order-book microstructure |
| [`market-microstructure-traditional`](crypto-research/agipro/market-microstructure-traditional/) | Equities/FX microstructure |

### Backtesting
| Skill | Trigger |
|---|---|
| [`backtrader`](crypto-research/agipro/backtrader/) / [`vectorbt`](crypto-research/agipro/vectorbt/) | Framework-specific method (reference) |
| [`walk-forward-validation`](crypto-research/agipro/walk-forward-validation/) ★ | Out-of-sample and walk-forward design |
| [`slippage-modeling`](crypto-research/agipro/slippage-modeling/) ★ | Cost realism in a backtest — research only |

### Portfolio, risk and sizing
| Skill | Trigger |
|---|---|
| [`portfolio-analytics`](crypto-research/agipro/portfolio-analytics/) ★ | Exposure, attribution, portfolio stats |
| [`risk-management`](crypto-research/agipro/risk-management/) | Risk framework (metrics: wshobson ★) |
| [`position-sizing`](crypto-research/agipro/position-sizing/) | Sizing math — **calculation only** |
| [`kelly-criterion`](crypto-research/agipro/kelly-criterion/) | Kelly and fractional-Kelly sizing |
| [`exit-strategies`](crypto-research/agipro/exit-strategies/) | Exit rule design — planning only |
| [`trade-journal`](crypto-research/agipro/trade-journal/) | Journal structure and review (see also marian ★) |

### Instruments
| Skill | Trigger |
|---|---|
| [`options-pricing`](crypto-research/agipro/options-pricing/) ★ | Black-Scholes, greeks, IV mechanics |
| [`fixed-income`](crypto-research/agipro/fixed-income/) ★ | Duration, convexity, yield math |

### DeFi and on-chain — strictly read-only
| Skill | Trigger |
|---|---|
| [`dex-pool-analysis`](crypto-research/agipro/dex-pool-analysis/) | Pool composition and depth analysis |
| [`liquidity-analysis`](crypto-research/agipro/liquidity-analysis/) | Liquidity conditions |
| [`lp-math`](crypto-research/agipro/lp-math/) | AMM/LP mathematics |
| [`impermanent-loss`](crypto-research/agipro/impermanent-loss/) | IL quantification |
| [`yield-analysis`](crypto-research/agipro/yield-analysis/) | Yield source decomposition |
| [`token-economics`](crypto-research/agipro/token-economics/) | Emissions, supply, incentives |
| [`token-holder-analysis`](crypto-research/agipro/token-holder-analysis/) | Holder distribution, insider patterns |
| [`whale-tracking`](crypto-research/agipro/whale-tracking/) | Large-holder flow observation |
| [`sybil-detection`](crypto-research/agipro/sybil-detection/) | Sybil/wash pattern detection |
| [`pumpfun-mechanics`](crypto-research/agipro/pumpfun-mechanics/) | Bonding-curve mechanics — descriptive |

### Public read-only data APIs
| Skill | Trigger | Note |
|---|---|---|
| [`coingecko-api`](crypto-research/agipro/coingecko-api/) | Prices, market caps | Public read endpoints only |
| [`defillama-api`](crypto-research/agipro/defillama-api/) | TVL, protocol data | Public read endpoints only |
| [`dexscreener-api`](crypto-research/agipro/dexscreener-api/) | DEX pair data | Public read endpoints only |
| [`birdeye-api`](crypto-research/agipro/birdeye-api/) | Token/market data | Key is user-side; read endpoints only |
| [`helius-api`](crypto-research/agipro/helius-api/) | Solana **read** queries | Audited: no transaction submission imported |

### Prediction markets — research only
| Skill | Trigger |
|---|---|
| [`prediction-market-strategy`](crypto-research/agipro/prediction-market-strategy/) | Pricing and edge reasoning |
| [`kalshi-crypto-index-markets`](crypto-research/agipro/kalshi-crypto-index-markets/) | Market structure reference |
| [`kalshi-weather-markets`](crypto-research/agipro/kalshi-weather-markets/) | Market structure reference |

---

## Not available by design

Broker connections, wallets, private keys, order placement, DEX execution, copy trading,
bundle submission and live-order automation are **absent from this repository** — omitted at
import, not disabled. Do not search for them; do not reconstruct them. See
[`../SECURITY.md`](../SECURITY.md) and [`../SOURCES.lock.md`](../SOURCES.lock.md).

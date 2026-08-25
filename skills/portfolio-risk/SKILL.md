---
name: portfolio-risk-category
description: Category router for portfolio construction, concentration, exposure and risk analytics. Use for holdings reviews, drawdown and VaR questions, position sizing as analysis, and risk-metric interpretation.
category: portfolio-risk
read_only: true
---

# Portfolio and risk — category router

This directory holds no analysis of its own. It routes portfolio and risk requests to the
authoritative imported skill and names which version wins when several overlap.

Read [`../orchestration/finance-research-orchestrator/SKILL.md`](../orchestration/finance-research-orchestrator/SKILL.md)
first. Everything below inherits its evidence rules.

## Routing table

| Question | Authoritative skill | Notes |
|---|---|---|
| Risk metrics: Sharpe, Sortino, VaR, CVaR, drawdown, beta | [`../quantitative/wshobson/risk-metrics-calculation/`](../quantitative/wshobson/risk-metrics-calculation/) | **Authoritative** for metric definitions and pitfalls |
| Risk-management framework and limits | [`../quantitative/wshobson/risk-manager-methodology/`](../quantitative/wshobson/risk-manager-methodology/) | Supporting: [`../crypto-research/agipro/risk-management/`](../crypto-research/agipro/risk-management/) |
| Portfolio analytics, attribution, exposure | [`../crypto-research/agipro/portfolio-analytics/`](../crypto-research/agipro/portfolio-analytics/) | Cross-asset, method-level |
| Portfolio construction and allocation | [`../llmquant/llmquant-portfolio/`](../llmquant/llmquant-portfolio/), [`../llmquant/llmquant-portfolio-lab/`](../llmquant/llmquant-portfolio-lab/) | `dependency-required` |
| Risk framing for a research memo | [`../llmquant/llmquant-risk/`](../llmquant/llmquant-risk/) | `dependency-required` |
| Concentration and overlap | [`../trading-process/marian/trade-construction/portfolio-concentration/`](../trading-process/marian/trade-construction/portfolio-concentration/) | Correlated-bet detection |
| Position sizing (**analysis only**) | [`../trading-process/marian/trade-construction/position-sizing/`](../trading-process/marian/trade-construction/position-sizing/) | Sizing math: [`../crypto-research/agipro/position-sizing/`](../crypto-research/agipro/position-sizing/), [`kelly-criterion`](../crypto-research/agipro/kelly-criterion/) |
| Risk/reward sanity check | [`../trading-process/marian/trade-construction/risk-reward-sanity-check/`](../trading-process/marian/trade-construction/risk-reward-sanity-check/) | |
| Correlation and diversification | [`../crypto-research/agipro/correlation-analysis/`](../crypto-research/agipro/correlation-analysis/) | Pairs: [`cointegration-analysis`](../crypto-research/agipro/cointegration-analysis/) |
| Volatility estimation and forecasting | [`../crypto-research/agipro/volatility-modeling/`](../crypto-research/agipro/volatility-modeling/) | |
| Full portfolio risk review workflow | [`../trading-process/marian/workflows/portfolio-risk-review/`](../trading-process/marian/workflows/portfolio-risk-review/) | Multi-step, use for "review my portfolio" |
| Exit rules and scenario planning | [`../crypto-research/agipro/exit-strategies/`](../crypto-research/agipro/exit-strategies/) | Planning only — no orders |
| Ongoing position review | [`../trading-process/marian/position-oversight/position-management/`](../trading-process/marian/position-oversight/position-management/) | Analysis only |

## Overlap resolution

Three sources cover risk. When they disagree, prefer in this order:

1. **wshobson `risk-metrics-calculation`** — metric definitions, formulas, failure modes.
2. **agipro `risk-management` / `portfolio-analytics`** — implementation method and
   cross-asset treatment.
3. **LLMQuant `llmquant-risk`** — narrative framing for a memo, but its data path is
   unavailable here.

## Scope limit — analysis, never execution

Position sizing, exit rules and rebalancing plans produced here are **numbers and checklists
for a human to act on**. This library places no orders, connects to no broker and holds no
wallet. Never turn a sizing output into an order instruction or an execution script.

Ask for real weights, cost basis and account currency. Do not guess a portfolio's
composition, and do not invent prices to mark it to market — say what is missing instead.

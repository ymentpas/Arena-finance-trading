---
name: finance-research-category
description: Category router for broad finance and equity research — equities, ETFs, macro, rates, FX, credit, commodities, events and market intelligence. Points to the authoritative skill for each research angle.
category: finance-research
read_only: true
---

# Finance research — category router

This directory holds no analysis of its own. It routes finance and equity research requests
to the authoritative imported skill, so the orchestrator has one predictable entry point per
research angle.

Read [`../orchestration/finance-research-orchestrator/SKILL.md`](../orchestration/finance-research-orchestrator/SKILL.md)
first for evidence discipline. Everything below inherits it.

## Routing table

| Research angle | Authoritative skill | Notes |
|---|---|---|
| Single-stock / equity research | [`../llmquant/llmquant-equities/`](../llmquant/llmquant-equities/) | Valuation, comparison, memos, sell discipline |
| ETFs and fund selection | [`../llmquant/llmquant-etfs/`](../llmquant/llmquant-etfs/) | |
| Options and derivatives | [`../llmquant/llmquant-options/`](../llmquant/llmquant-options/), [`../llmquant/llmquant-equity-derivatives/`](../llmquant/llmquant-equity-derivatives/) | Pricing method: [`../crypto-research/agipro/options-pricing/`](../crypto-research/agipro/options-pricing/) |
| Macro and policy | [`../llmquant/llmquant-macro/`](../llmquant/llmquant-macro/) | Event framing: [`../trading-process/marian/market-context/macro-event-analysis/`](../trading-process/marian/market-context/macro-event-analysis/) |
| Rates and FX | [`../llmquant/llmquant-rates-fx/`](../llmquant/llmquant-rates-fx/) | |
| Credit | [`../llmquant/llmquant-credit/`](../llmquant/llmquant-credit/) | Instrument math: [`../crypto-research/agipro/fixed-income/`](../crypto-research/agipro/fixed-income/) |
| Commodities | [`../llmquant/llmquant-commodities/`](../llmquant/llmquant-commodities/) | |
| Corporate / macro events | [`../llmquant/llmquant-events/`](../llmquant/llmquant-events/) | Earnings prep: [`../trading-process/marian/market-context/earnings-preview/`](../trading-process/marian/market-context/earnings-preview/) |
| Market intelligence, news flow | [`../llmquant/llmquant-market-intelligence/`](../llmquant/llmquant-market-intelligence/) | Sentiment method: [`../crypto-research/agipro/sentiment-analysis/`](../crypto-research/agipro/sentiment-analysis/) |
| Investor mental models | [`../llmquant/llmquant-investor-lenses/`](../llmquant/llmquant-investor-lenses/) | Munger, Buffett, Marks-style lenses |
| Market regime / context | [`../trading-process/marian/market-context/market-regime-analysis/`](../trading-process/marian/market-context/market-regime-analysis/) | Quant method: [`../crypto-research/agipro/regime-detection/`](../crypto-research/agipro/regime-detection/) |
| Idea generation, watchlists, catalysts | [`../trading-process/marian/idea-discovery/`](../trading-process/marian/idea-discovery/) | |
| Thesis and evidence quality | [`../trading-process/marian/thesis-validation/`](../trading-process/marian/thesis-validation/) | |
| Where to get the data | [`../london-strategic-edge/SKILL.md`](../london-strategic-edge/SKILL.md) | Catalogue, candles, fundamentals, calendar |

## Dependency warning

Every `llmquant-*` skill is written against the **LLMQuant Data** connector, which is **not
available in this environment**. They are marked `dependency-required` in
[`../INDEX.md`](../INDEX.md).

Use them for their analytical structure — what to look at, in what order, what invalidates a
conclusion — and source the actual numbers elsewhere (the user's own data, an allowed tool,
London Strategic Edge). Never present a workflow's example output as retrieved data, and
never ask the user for LLMQuant credentials.

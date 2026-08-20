# SOURCES.lock.md

Provenance record for every third-party source considered during installation: what was
imported, what was omitted, and why. Licences are archived in
[`third-party-licenses/`](third-party-licenses/).

Installation date: **2026-08-21**. Commits are the exact revisions audited and copied.

Import method: shallow clone into `/tmp`, audit, selective copy of approved folders,
deletion of the temporary clone. No vendor `.git` directory, no `.github/workflows`, no build
output and no package cache was copied. No downloaded script was executed and no package was
installed.

---

## Summary

| Source | Licence | Status | Imported | Omitted |
|---|---|---|---|---|
| marian2js/trading-skills | MIT | ✅ imported | 18 skills + `_lib` | 1 skill (`live-trade/etoro`) |
| wshobson/agents | MIT | ✅ partial | 2 skills + 2 agent methodologies | all other plugins |
| agent-skills-hub/agent-skills-hub | MIT | ✅ partial | 1 skill (`quant-analyst`) | rest of repository |
| LLMQuant/skills | MIT | ✅ imported | 18 category skills | repo-level extras |
| agiprolabs/claude-trading-skills | MIT | ✅ partial | 45 skills | 22 skills |
| lzwme/finance-quant-skills | ❌ none found | ⛔ **skipped** | nothing | everything |

---

## 1. marian2js/trading-skills

- **URL**: https://github.com/marian2js/trading-skills
- **Commit**: `f1ae7d481154b49192681187cb08d39d7e2d4524`
- **Licence**: MIT → [`third-party-licenses/marian2js-trading-skills-LICENSE.txt`](third-party-licenses/marian2js-trading-skills-LICENSE.txt)
- **Destination**: `skills/trading-process/marian/`

**Imported paths** (`skills/` upstream, complete folders with their `references/`):

`_lib/` · `idea-discovery/` (catalyst-map, watchlist-review) · `market-context/`
(earnings-preview, macro-event-analysis, market-regime-analysis) · `position-oversight/`
(position-management) · `review-learning/` (journal-pattern-analyzer, post-trade-review) ·
`thesis-validation/` (evidence-gap-check, thesis-validation) · `trade-construction/`
(execution-plan-check, portfolio-concentration, position-sizing, risk-reward-sanity-check) ·
`workflows/` (earnings-trade-prep, portfolio-risk-review, post-trade-debrief, pre-trade-check)

**Omitted paths**

| Path | Reason |
|---|---|
| `skills/live-trade/etoro/` | Broker execution — outside scope, explicitly excluded |
| `scripts/`, `Makefile`, `catalog.json`, `docs/`, `CHANGELOG.md`, `VERSION` | Repository tooling, not skill content |

**Note** — `_lib/calculations.py` (9 KB) was kept because three imported skills reference it
by relative link (`skills/_lib/calculations.py`) from their `references/calculation-helpers.md`.
It is **reference material for its formulas**, not an application: nothing installs it, runs
it, or depends on it at runtime.

## 2. wshobson/agents

- **URL**: https://github.com/wshobson/agents
- **Commit**: `367cb6a4a182cf7e9b0a17c9429f7411ddd9cf35`
- **Licence**: MIT → [`third-party-licenses/wshobson-agents-LICENSE.txt`](third-party-licenses/wshobson-agents-LICENSE.txt)
- **Destination**: `skills/quantitative/wshobson/`

**Imported paths** — from `plugins/quantitative-trading/` only:

| Upstream | Local | Note |
|---|---|---|
| `skills/backtesting-frameworks/` | `backtesting-frameworks/` | Complete folder incl. `references/details.md` |
| `skills/risk-metrics-calculation/` | `risk-metrics-calculation/` | Complete folder incl. `references/details.md` |
| `agents/quant-analyst.md` | `quant-analyst-methodology/SKILL.md` | Renamed for consistent routing |
| `agents/risk-manager.md` | `risk-manager-methodology/SKILL.md` | Renamed for consistent routing |

**Omitted** — every other plugin in the repository (unrelated domains), plus `docs/`,
`tools/`, `Makefile`, `ARCHITECTURE.md` and the repository's own `AGENTS.md`/`CLAUDE.md`
(which would conflict with this repository's root policy).

## 3. agent-skills-hub/agent-skills-hub

- **URL**: https://github.com/agent-skills-hub/agent-skills-hub
- **Commit**: `40b657cc4dd50b0f5eb45551480efbeabcdc15a7`
- **Licence**: MIT → [`third-party-licenses/agent-skills-hub-LICENSE.txt`](third-party-licenses/agent-skills-hub-LICENSE.txt)
- **Destination**: `skills/quantitative/agent-skills-hub/quant-analyst/`

**Imported**: `skills/quant-analyst/SKILL.md` only (4 KB).

**Omitted**: the entire remainder of the repository — 71 MB including `assets/`
(a 4.4 MB `.mov`, PNGs), `skills/last30days/assets/` (~14 MB of media), `bin/`, `lib/`,
`data/`, `node_modules` manifests and hundreds of unrelated skills. No full clone of this
repository exists inside the connected repository, per the workspace budget.

**Duplication note**: this repository also ships `backtesting-frameworks` and
`risk-metrics-calculation`. They were **not** imported — the wshobson versions are
authoritative here, as instructed.

## 4. LLMQuant/skills

- **URL**: https://github.com/LLMQuant/skills
- **Commit**: `1918237467c2dff4cc97a18ebc0892dfd46e8129`
- **Licence**: MIT → [`third-party-licenses/llmquant-skills-LICENSE.txt`](third-party-licenses/llmquant-skills-LICENSE.txt)
- **Destination**: `skills/llmquant/`

**Imported**: all 18 category skills with their `workflows/`, `scripts/` and `assets/`
subfolders (530 KB total) — commodities, credit, crypto, data, equities,
equity-derivatives, etfs, events, investor-lenses, macro, market-intelligence, options,
portfolio, portfolio-lab, prediction-markets, rates-fx, risk, strategies.

**Omitted**: `assets/` at repository root (1.7 MB header image), `templates/`,
`CONTRIBUTING.md`, `README*.md`.

**⚠️ `dependency-required`** — every one of these skills declares
`input_data_source: LLMQuant Data`, a connector **not available in this environment**. They
are labelled as such in [`skills/INDEX.md`](skills/INDEX.md). Their analytical structure is
usable; their data path is not. Arena must not claim to have this connector and must never
ask the user for LLMQuant credentials.

**Audit**: scanned for execution material. The only matches were prose — "prime brokerage"
in `llmquant-strategies/workflows/equity-long-short.md`, "executive compensation… broker pay"
in `llmquant-investor-lenses/workflows/charlie-munger.md`. No execution capability present.

## 5. agiprolabs/claude-trading-skills

- **URL**: https://github.com/agiprolabs/claude-trading-skills
- **Commit**: `f3fa5a2a20719cc275d8561ec8ae59a135f36948`
- **Licence**: MIT → [`third-party-licenses/agiprolabs-claude-trading-skills-LICENSE.md`](third-party-licenses/agiprolabs-claude-trading-skills-LICENSE.md)
- **Destination**: `skills/crypto-research/agipro/`
- **Result**: **45 of 67 skills imported, 22 omitted.**

**Audit method**: every skill folder was scanned for `private_key` / `seed phrase` /
`mnemonic` / `Keypair` / transaction signing / `sendTransaction` / order submission /
bundle submission / copy trading / swap execution. Every match was then read in context to
separate genuine execution capability from prose (e.g. backtest documentation explaining
"if you place a market order based on close, it executes at the next bar's open"). API-oriented
skills were additionally checked for write/trade endpoints versus purely public read endpoints.

### Imported (45)

**Data & signal** — ohlcv-processing, feature-engineering, signal-classification,
custom-indicators, pandas-ta, ta-lib, trading-visualization, sentiment-analysis
**Statistics** — correlation-analysis, cointegration-analysis, mean-reversion,
regime-detection, volatility-modeling, market-microstructure,
market-microstructure-traditional
**Backtesting** — backtrader, vectorbt, walk-forward-validation, slippage-modeling
**Portfolio & risk** — portfolio-analytics, risk-management, position-sizing,
kelly-criterion, exit-strategies, trade-journal
**Instruments** — options-pricing, fixed-income
**DeFi / on-chain (read-only)** — dex-pool-analysis, liquidity-analysis, lp-math,
impermanent-loss, yield-analysis, token-economics, token-holder-analysis, whale-tracking,
sybil-detection, pumpfun-mechanics
**Public read-only APIs** — coingecko-api, defillama-api, dexscreener-api, birdeye-api,
helius-api
**Prediction markets (research)** — prediction-market-strategy,
kalshi-crypto-index-markets, kalshi-weather-markets

### Omitted — unsafe (15)

| Skill | Reason |
|---|---|
| `dex-execution` | DEX order/swap execution |
| `copy-trading` | Copy trading — explicitly out of scope |
| `jito-bundles` | Bundle submission to the chain |
| `raptor-dex` | DEX execution service |
| `shredstream` | Execution-path transaction streaming |
| `solana-tx-building` | Constructs and signs transactions |
| `solana-rpc` | Documents `sendTransaction` — submits signed transactions |
| `rl-execution` | Automated execution policy |
| `mev-analysis` | `submit_jito_bundle`, `sendTransaction`, bundle submission |
| `solanatracker-api` | `references/raptor_setup.md`: `PRIVATE_KEY`, `POST /swap`, "Submit Transaction" |
| `yellowstone-grpc` | Execution-adjacent mempool infrastructure; not in the requested scope |
| `kalshi-api` | `KALSHI_PRIVATE_KEY_PATH`, `POST /portfolio/orders` — write/trade endpoints |
| `polymarket-api` | `POLYMARKET_PRIVATE_KEY` wallet key, CLOB order submission |
| `wallet-profiling` | Built around a copy-trade evaluation framework |
| `strategy-framework` | Contains a "Copy Trading / Wallet Following" strategy section |

### Omitted — out of scope (7)

Tax, accounting and regulatory skills, none of which appear in the requested import list:
`crypto-tax-export`, `tax-liability-tracking`, `tax-loss-harvesting`, `wash-sale-detection`,
`cost-basis-engine`, `trade-accounting`, `regulatory-reporting`.

These are read-only and not unsafe — they were left out to keep the library focused on the
requested research scope. `tax-loss-harvesting` additionally points the reader to
`dex-execution` and `jupiter-swap` as its execution step. **They can be added on request.**

**Also omitted**: repository root `claude-trading-skills.gif` (11 MB), `tests/`,
`examples.md`, `trading-skills.md`, `CONTRIBUTING.md`.

**Also removed after import**: four generated sample charts in `trading-visualization/`
(`candlestick.png`, `equity_drawdown.png`, `return_distribution.png`, `trade_markers.png`,
~505 KB). They are not referenced by any relative link in the skill and fall under the
workspace-budget prohibition on generated chart collections. The skill's `SKILL.md`,
`references/` and `scripts/` are intact.

**Note on imported `scripts/*.py`**: approved skill folders were preserved complete, so they
retain their `references/` and `scripts/` files, which relative links depend on. These are
**reference material**. Nothing is installed or executed; this repository is not a Python
project.

## 6. lzwme/finance-quant-skills — ⛔ SKIPPED

- **URL**: https://github.com/lzwme/finance-quant-skills
- **Commit inspected**: `b03516e6d6e6f839b3ce3fb8ad529eb5e6b7f874`
- **Status**: **nothing imported — skipped pending licence confirmation**

**Reason**: the repository contains **no `LICENSE` file**. `package.json` declares no
`license` field and `pyproject.toml` contains no licence metadata. The only statement is one
line in `README.md`: *"This project is released under the MIT license."* — with no licence
text and no named copyright holder.

Per the installation policy (*"If a source lacks a usable license, do not copy it; record it
as skipped pending license confirmation"*), a bare README sentence was judged **not a usable
licence grant**. No file from this repository was copied.

**To unblock**: ask the maintainer to add a proper `LICENSE` file, or obtain written
confirmation of the terms. Then re-audit — and in any case **never import its MiniQMT or any
other live-order/client-control material**, which is out of scope regardless of licence.

---

## Authored content (no third party)

Written for this repository, not imported: [`AGENTS.md`](AGENTS.md),
[`README.md`](README.md), [`SECURITY.md`](SECURITY.md), this file,
[`skills/INDEX.md`](skills/INDEX.md),
[`skills/orchestration/finance-research-orchestrator/SKILL.md`](skills/orchestration/finance-research-orchestrator/SKILL.md),
[`skills/finance-research/SKILL.md`](skills/finance-research/SKILL.md),
[`skills/portfolio-risk/SKILL.md`](skills/portfolio-risk/SKILL.md),
[`skills/london-strategic-edge/SKILL.md`](skills/london-strategic-edge/SKILL.md),
[`skills/pine-script/SKILL.md`](skills/pine-script/SKILL.md),
[`skills/tradingview-validation/SKILL.md`](skills/tradingview-validation/SKILL.md),
[`pine/README.md`](pine/README.md), [`reports/README.md`](reports/README.md).

**Documentation consulted** for the authored skills (read-only, nothing downloaded into the
repository), on 2026-08-21:

- https://londonstrategicedge.com/
- https://londonstrategicedge.com/api-documentation/
- https://londonstrategicedge.com/free-market-data-api/
- https://londonstrategicedge.com/backtesting-free/
- https://github.com/londonstrategicedge/lse-data (README only — the client is **not**
  vendored, installed or wrapped here)
- https://www.tradingview.com/pine-script-docs/ (Pine Script v6 reference)

No API key was requested, received or stored at any point during this installation.

# reports/

Research notes, validation reports and analysis summaries produced during a session.

## Conventions

- Markdown only, one file per report: `YYYY-MM-DD-<subject>.md`
  (e.g. `2026-08-21-ema-cross-validation.md`).
- Open every report with **symbol, timeframe, data source and as-of date**. A report without
  provenance cannot be re-checked later, which makes it worthless.
- Label each claim as fact, retrieved data, calculation, hypothesis or opinion.
- Mark any illustrative figure `ILLUSTRATIVE — NOT A RESULT` on the same line as the numbers.

Validation report template: [`../skills/tradingview-validation/SKILL.md`](../skills/tradingview-validation/SKILL.md) §7.

## Rules

- **No credentials** in a report, ever — including inside a pasted command or error message.
- **No raw data.** Keep derived tables and summaries. Tick data, CSV/Parquet archives and
  chart-image collections do not belong in this repository (see
  [`../AGENTS.md`](../AGENTS.md) §9), and provider terms may forbid redistributing them.
- **No invented numbers.** If a figure was not computed from real data, it does not go in a
  report as a result.

# FX Hedging — capability

Compares the USD outcome of hedging a euro receivable with a **forward**, a **money-market hedge**, and a **put option**, against staying unhedged.

## Scenario (10: craft-whiskey)

A U.S. craft-whiskey distiller has invoiced a French luxury-hotel group **EUR 2,100,000**, received in **91 days**. Base currency USD; pair EURUSD. The risk is a falling euro.

## Files in this folder

| File | Stage | What it is | Status |
|---|---|---|---|
| `spec.md` | 2 | Model specification: tabs, named ranges, formulas, validation checks, hand-check values | Committed 2026-10-30 (v1.0); Stage 3 audit revisions are listed in its change log |
| `model.xlsx` | 3–4 | Workbook built from `spec.md`, then populated with live market data | Built 2026-11-13 (Stage 3); the spec version it was built from is on its `Cover` tab. Placeholder inputs until Stage 4 |

## How to use the model

1. Change inputs only on the `Inputs` tab: yellow cells are inputs, blue-font cells are assumptions. Record the source and as-of date for each on `Inputs` and `Notes`.
2. Read results on `Sensitivity`: `tbl_Summary` for the four strategies at a glance, the ±5% grid and its chart below it. The hedge mechanics are on `Forward`, `MoneyMarket` and `Options`.
3. Open `Checks` before using any number. Every row should read "As expected? Yes". A FLAG is explained in the build audit or the market-data memo, never forced to pass.

## Related documents

- Engagement brief: `docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md`
- Build audit (Stage 3): [`analysis/2026-11-13-craft-whiskey-build-audit-analysis.md`](../../analysis/2026-11-13-craft-whiskey-build-audit-analysis.md)
- Market-data memo (Stage 4): `data/YYYY-MM-DD-craft-whiskey-market-data-memo.md`
- Validation + decision memo (Stage 5): `analysis/…-validation-analysis.md`, `docs/decisions/…-hedge-decision-memo.md`
## Used in

- [Engagement: craft-whiskey (Scenario 10)](../../docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md)

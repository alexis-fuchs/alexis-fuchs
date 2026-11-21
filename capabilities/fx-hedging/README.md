# FX Hedging — capability

Compares the USD outcome of hedging a euro receivable with a **forward**, a **money-market hedge**, and a **put option**, against staying unhedged.

## Scenario (10: craft-whiskey)

A U.S. craft-whiskey distiller has invoiced a French luxury-hotel group **EUR 2,100,000**, received in **91 days**. Base currency USD; pair EURUSD. The risk is a falling euro.

## Files in this folder

| File | Stage | What it is | Status |
|---|---|---|---|
| `spec.md` | 2 | Model specification: tabs, named ranges, formulas, validation checks, hand-check values | Committed 2026-10-30 (v1.0); revised in the Stage 3 audit (v1.1–v1.5) and at Stage 4 (v1.6); see its change log |
| `model.xlsx` | 3–4 | Workbook built from `spec.md`, then populated with live market data | Built 2026-11-13 (Stage 3); populated with live market data of 2026-11-20 (Stage 4); validated independently 2026-11-20 (Stage 5). The spec version it was built from is on its `Cover` tab |

## How to use the model

1. Change inputs only on the `Inputs` tab: yellow cells are inputs, blue-font cells are assumptions. Record the source, data timestamp and retrieval time for each on `Inputs` (the `Cover` reads them from there) and the URL on `Notes`.
2. Read results on `Sensitivity`: `tbl_Summary` for the four strategies at a glance, the ±5% grid and its chart below it. The hedge mechanics are on `Forward`, `MoneyMarket` and `Options`.
3. Start with the `Cover`'s **Read first** line, which points to Checks V1 (the bank's quote vs. parity). Then open `Checks` before using any number. Every row should read "As expected? Yes". A FLAG is explained in the build audit or the market-data memo, never forced to pass.

## Related documents

- Engagement brief: `docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md`
- Build audit (Stage 3): [`analysis/2026-11-13-craft-whiskey-build-audit-analysis.md`](../../analysis/2026-11-13-craft-whiskey-build-audit-analysis.md)
- Market-data memo (Stage 4): [`data/2026-11-20-craft-whiskey-market-data-memo.md`](../../data/2026-11-20-craft-whiskey-market-data-memo.md)
- Validation (Stage 5): [`analysis/2026-11-20-craft-whiskey-validation-analysis.md`](../../analysis/2026-11-20-craft-whiskey-validation-analysis.md)
- Decision memo (Stage 5): [`docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md`](../../docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md)
## Used in

- [Engagement: craft-whiskey (Scenario 10)](../../docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md) → [decision](../../docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md)

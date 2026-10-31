# FX Hedging — capability

Compares the USD outcome of hedging a euro receivable with a **forward**, a **money-market hedge**, and a **put option**, against staying unhedged.

## Scenario (10: craft-whiskey)

A U.S. craft-whiskey distiller has invoiced a French luxury-hotel group **EUR 2,100,000**, received in **91 days**. Base currency USD; pair EURUSD. The risk is a falling euro.

## Files in this folder

| File | Stage | What it is | Status |
|---|---|---|---|
| `spec.md` | 2 | Model specification: tabs, named ranges, formulas, validation checks, hand-check values | Committed 2026-10-30 |
| `model.xlsx` | 3–4 | Workbook built from `spec.md`, then populated with live market data | Not yet built |

## How to use the model

1. Change inputs only on the `Inputs` tab (blue cells). Record the source and as-of date for each.
2. Read results on `Summary`; the full outcome range is on `Scenarios`.
3. Open `Checks` before using any number. Overall must not be FAIL. A WARN needs a written comment.

## Related documents

- Engagement brief: `docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md`
- Build audit (Stage 3): `analysis/YYYY-MM-DD-craft-whiskey-build-audit-analysis.md`
- Market-data memo (Stage 4): `data/YYYY-MM-DD-craft-whiskey-market-data-memo.md`
- Validation + decision memo (Stage 5): `analysis/…-validation-analysis.md`, `docs/decisions/…-hedge-decision-memo.md`
## Used in

- [Engagement: craft-whiskey (Scenario 10)](../../docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md)

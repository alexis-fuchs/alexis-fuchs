---
type: analysis
title: "Stage 3 Build Audit: AI-Generated Hedge Workbook vs. Spec"
scenario: craft-whiskey
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-13
status: committed          # committed | superseded
llm_used: "Claude"
---

# Stage 3 Build Audit: AI-Generated Hedge Workbook vs. Spec

**Created by:** Alexis Fuchs  
**Date Created:** 2026-11-13  
**Version:** 1.0  
**LLM Used:** Claude (generated the workbook from the spec, ran the audit scripts, and drafted this note; reviewed by the author)  
**Workbook:** [`capabilities/fx-hedging/model.xlsx`](../capabilities/fx-hedging/model.xlsx) · **Spec:** [`capabilities/fx-hedging/spec.md`](../capabilities/fx-hedging/spec.md)

---

## Summary

I generated the workbook from `spec.md` v1.0 as written, then audited it against the build contract and the spec's own check figures (§5.1, §5.2, §7.1). The first build got the numbers right: every named output and all 11 grid rows match an independent recomputation to the cent. The problems were in what the spec failed to say. I found **five defects**, traced each one to the spec line that allowed it, fixed the spec (v1.1–v1.5), and regenerated the workbook after each fix. **Final state: 10 PASS, 2 FLAG.** Both FLAGs (V1, V8) are the expected parity failures caused by the placeholder forward. They are reported below, not forced to pass.

## How the build and the audit were run

- **Build:** Claude, given `capabilities/fx-hedging/spec.md` v1.0 (commit `d5f6046`) with the instruction that every build-contract item is binding. It wrote the `.xlsx` in Python (openpyxl) and recalculated it in LibreOffice. Prompt in `prompt-log.md`.
- **Audit, independent of the workbook's formulas:**
  1. A script that reads every cell, maps each named range to its cell, and flags any green or gray cell that is not a formula, any typed number outside `Inputs`, and any formula that points at an input by cell address.
  2. A plain-Python recomputation of every named output and every grid cell, compared with the workbook's values.
  3. Stress tests: `S0_in` changed to 1.0900, then `K_PUT` changed to 1.1200, then recalculated.
  4. Planted defects: a pasted number, a sign error and an error cell, to prove each check can actually fail.
  5. Each tab rendered to an image to inspect the layout and the chart.

---

## Findings: defects found and fixed

Each fix changed the spec first and then regenerated the workbook, in its own commit. Nothing was patched by hand in the workbook.

### 1. The call had no payoff as a function of `S_T` (contract item 5)

- **Checked:** contract item 5 says "put and call with premium cost in USD and proceeds as a function of `S_T`."
- **Found:** the v1.0 build showed the call's premium (−$41,790.00 today, −$42,212.54 at settlement) but had no call payoff column. The cause was in the spec: §4 Step 4 said the call was "parity check only," and the AI followed it.
- **Did:** spec v1.1 adds `PREM_PUT_USD`, `PREM_CALL_USD` and `USD_CALL(S_T) = MAX(0, S_T − K_CALL) × FC_AMT + FV_PREM_CALL`, plus a put-and-call payoff table on `Options`. The rebuilt `USD_CALL` runs from −$42,212.54 (−5% to 0%) to +$75,093.46 (+5%), which matches my hand calculation.
- **Commit:** `Stage 3 fix 1 …`

### 2. V10 returned `#NAME?`, and V9 reported zero errors anyway

- **Checked:** recalculated the workbook and read every check result.
- **Found:** V10 (every green and gray cell is a formula) showed `#NAME?` in `Checks!C26`, `E26` and `G26`. The generator wrote `ISFORMULA`, an Excel 2013+ function that has to be stored as `_xlfn.ISFORMULA`. V9 (no error cells "on every tab") still read **0 / PASS**, because the build left the `Checks` tab out of its scan to avoid a circular reference. As a test, I planted `=1/0` on `Checks`: the v1.0 V9 still read 0.
- **Did:** spec v1.2 makes V9 cover `Checks` (every row except V9's own row and the summary that depends on it) and adds an `_xlfn` build note to V10. After regenerating, V10 = 0 / PASS. The same planted error now makes V9 = 1 / FLAG. Pasting a typed number over a grid cell makes V10 = 1 / FLAG.
- **Commit:** `Stage 3 fix 2 …`

### 3. Two put checks could not do their job (V3, V4)

- **Checked:** the stress tests.
- **Found:**
  - **V3** looked up the put's value at `K_PUT` on the grid. It FLAGged "K_PUT not on grid" whenever the strike sat between grid points (`K_PUT` = 1.1200, or `S0_in` = 1.0900). Stage 4 re-sets the strike near the live spot, so V3 would have failed for no reason.
  - **V4** compared `MIN(USD_PUT)` with `USD_FLOOR_PUT`, which is *defined* as `MIN(USD_PUT)`. It passed in every test because it could never fail.
- **Did:** spec v1.3 moves V3 to its own kink test on `Options`. The test prices the put one step below the strike, at the strike, and one step above, and compares each result with the piecewise payoff. V4 now compares `USD_FLOOR_PUT` with the true floor, `K_PUT × FC_AMT + FV_PREM_PUT`. With `K_PUT` = 1.1200, both now PASS (floor $2,320,817.87).
- **Commit:** `Stage 3 fix 3 …`

### 4. V5 failed on floating-point noise

- **Checked:** V5, whose rule is that the put never moves more than one grid step between adjacent rows.
- **Found:** on the placeholder inputs, V5 FLAGged: the largest change was **$23,461.2000000002** against a bound of **$23,461.20**. Above the strike, the change equals the bound exactly, and the spreadsheet's rounding pushed it 2/10,000,000,000 of a dollar over. The spec gave no tolerance.
- **Did:** spec v1.4 adds a $0.01 allowance, and V5 now PASSes. I confirmed it still catches real errors: flipping the premium's sign in one grid row makes V5 FLAG on a $62,364.26 jump.
- **Commit:** `Stage 3 fix 4 …`

### 5. `tbl_Summary` columns were named but never defined

- **Checked:** the summary table against spec §5.
- **Found:** the spec listed "locked/floor proceeds, up-front cost, upside kept" with no formulas, so the AI improvised. It wrote text such as "None — fully exposed" and chose its own definition of "upside kept" (at +5%).
- **Did:** spec v1.5 defines every column as a formula on the strategy's grid column: worst case (`MIN`), best case (`MAX`), proceeds at `S0_in`, up-front USD cost, and upside kept (best minus base). For the put the rebuilt table reads: worst $2,314,937.87, best $2,432,243.87, upside +$117,306.00, cost $30,870.00. The chart's budget line, which sat $8,142 from the money-market line and hid it, is now dashed gray.
- **Commit:** `Stage 3 fix 5 …`

---

## Findings: checked and confirmed

| # | What I checked | Result |
|---|----------------|--------|
| 6 | **All ten contract named ranges** (what Excel's Name Manager lists) | Each points to the `Inputs` row labelled with its own name (`FC_AMT` → `Inputs!C5` … `PREM_CALL` → `Inputs!C15`) and holds a typed input. 37 names in total. |
| 7 | **Formulas only** | 379 formulas. Every green and gray cell is a formula (scan, and V10 = 0). The only typed numbers outside `Inputs` are the 13 check figures and tolerances on `Checks`, in blue font and copied from spec §5.1–§5.2. No formula references an input by cell address. |
| 8 | **Base case and grid vs. spec §5.1 and §7.1** | The independent recomputation matched every named output and all 11 rows × 9 numeric columns within $0.005. V12's largest gap against §5.1 is $0.0021 (the spec rounds to cents). |
| 9 | **Sensitivity grid is formula-driven** | With `S0_in` changed from 1.1172 to 1.0900, all 11 rows moved (the −5% row went to `S_T` 1.035500, no hedge $2,174,550.00) and matched the recomputation. The chart series read the grid cells. |
| 10 | **Money market in three explicit steps** | Borrow €2,086,194.15 → convert to $2,330,696.10 → invest to $2,354,262.03. Loan repayment minus receivable = €0.00. V2 difference = 0. |

## Checks that fail, reported rather than forced

- **V1, forward vs. money-market parity: FLAG, +3.94% ($92,867.97).**
- **V8, put–call parity vs. quoted forward: FLAG, −3.68%.**

Both have the same cause. The indicative `F0_in` (1.1653) is a teaching placeholder 428–442 pips above both the CIP forward (1.121077) and the put–call-parity forward (1.122453). V7 shows the two market-implied forwards agree within 0.12%, so the model is consistent and the placeholder quote is the outlier. Until Stage 4 replaces it, the forward hedge looks about $93,000 better than the market would allow.

Under the stress test, V7 and V12 also FLAG when `S0_in` moves away from the placeholder values, and they should. V12 compares against the placeholder base case and will need re-baselining when Stage 4 loads live inputs.

## Still to verify

- [ ] Open `model.xlsx` in Microsoft Excel. The build was recalculated in LibreOffice, so confirm in Excel that `Checks` shows 10 PASS / 2 FLAG with no `#NAME?`, and that changing `S0_in` moves the `Sensitivity` grid and chart.

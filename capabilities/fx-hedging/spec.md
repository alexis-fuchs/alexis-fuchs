---
type: spec
engagement: fx-hedging
capability: fx-hedging
scenario: craft-whiskey
date: 2026-10-30
status: committed          # committed | superseded
brief: docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md
builds: capabilities/fx-hedging/model.xlsx
---

# FX Hedging Model — Specification (Scenario 10: craft-whiskey)

## 1. What the model answers

A U.S. craft-whiskey distiller has invoiced a French luxury-hotel group **EUR 2,100,000**, payable in **91 days**. The firm is long euros, so a **falling EURUSD** cuts its USD proceeds.

The workbook answers one question for the CFO: **how many U.S. dollars will we actually receive under each strategy, across a realistic range of settlement rates, and what does each strategy cost or give up?**

It compares four outcomes on the same basis (USD received on the settlement date):

1. **Unhedged** — convert at whatever spot is on the settlement date
2. **Forward** — sell EUR forward today at `F0_in`
3. **Money-market hedge (MMH)** — borrow EUR today, convert at spot, invest the USD
4. **Put option** — buy a EUR put struck at spot; premium paid today

The call premium is not a strategy for a receivable. It is used only for the put–call parity check (Section 7).

## 2. Workbook map

| Tab | Purpose | Contains |
|---|---|---|
| `README` | What this file is | Scenario, version, date, how to use, link to this spec |
| `Inputs` | Every number typed by a human | Named input cells with units, source, date, and Stage 4 status |
| `Calc` | Strategy math | Named derived cells; formulas only, no typed numbers |
| `Scenarios` | Outcome grid | USD received per strategy across settlement rates, plus key break-even rows |
| `Summary` | CFO view | One table: each strategy's locked/floor/expected USD, cost, and upside |
| `Checks` | Validation | Each check with formula, tolerance, and PASS / WARN / FAIL |

## 3. Inputs (tab `Inputs`)

All inputs are blue-font cells, each given a workbook-scope named range. Rates are decimals (4.00% entered as `0.04`). FX rates are **USD per 1 EUR**.

| Named range | Value | Units | Source and date | Stage 4 |
|---|---|---|---|---|
| `FC_AMT` | 2,100,000 | EUR | Scenario 10 (fixed) | **Never changes** |
| `T_DAYS` | 91 | days | Scenario 10 (fixed) | **Never changes** |
| `BASIS` | 360 | days/yr | Money-market convention (ACT/360), assumption | Keep |
| `S0_in` | 1.1172 | USD/EUR | ECB euro reference rate, 30 Oct 2026 | Replace with live spot |
| `R_USD` | 0.0400 | decimal, annual | Federal Reserve H.15, 3-month Treasury constant maturity, 29 Oct 2026 (latest published 30 Oct 2026) | Replace with live rate |
| `R_FC` | 0.02618 | decimal, annual | 3-month Euribor fixing, 28 Oct 2026 (latest published 30 Oct 2026), EMMI via euribor-rates.info | Replace with live rate |
| `F0_in` | 1.1653 | USD/EUR | Scenario 10 indicative forward, set 2026-10-18 for teaching — **placeholder** | Replace with live or CIP-implied forward; comment on gap |
| `K_PUT` | 1.1172 | USD/EUR | Scenario 10: strike at spot (= `S0_in` on 30 Oct 2026) | Keep at or near spot |
| `K_CALL` | 1.1172 | USD/EUR | Scenario 10: strike at spot | Keep at or near spot |
| `P_PUT` | 0.0147 | USD per EUR | Scenario 10 (given) | **Kept** |
| `P_CALL` | 0.0199 | USD per EUR | Scenario 10 (given) | **Kept** |

Each input row also carries three text columns: **Source**, **As-of date**, and **Notes**, so Stage 4 can overwrite values without losing provenance.

## 4. Calculations (tab `Calc`)

Every cell here is a formula that references named ranges only. No typed numbers.

### 4.1 Time and growth factors

| Named range | Formula | Meaning |
|---|---|---|
| `T_YRS` | `=T_DAYS/BASIS` | Year fraction |
| `GROW_USD` | `=1+R_USD*T_YRS` | $1 today grows to this at settlement |
| `GROW_FC` | `=1+R_FC*T_YRS` | €1 today grows to this at settlement |

### 4.2 Reference points

| Named range | Formula | Meaning |
|---|---|---|
| `BUDGET_USD` | `=FC_AMT*S0_in` | USD value of the receivable at today's spot (the budget line) |
| `F_CIP` | `=S0_in*GROW_USD/GROW_FC` | Forward implied by covered interest parity |
| `FWD_GAP` | `=F0_in-F_CIP` | Quoted forward minus CIP forward (USD/EUR) |
| `FWD_GAP_PCT` | `=F0_in/F_CIP-1` | Same gap as a percentage |

### 4.3 Forward hedge

| Named range | Formula | Meaning |
|---|---|---|
| `USD_FWD` | `=FC_AMT*F0_in` | Locked USD received, independent of settlement spot |

### 4.4 Money-market hedge

| Named range | Formula | Meaning |
|---|---|---|
| `MMH_EUR_BORROW` | `=FC_AMT/GROW_FC` | EUR borrowed today; the receivable repays principal + interest exactly |
| `MMH_USD_T0` | `=MMH_EUR_BORROW*S0_in` | USD received today on conversion |
| `USD_MMH` | `=MMH_USD_T0*GROW_USD` | USD at settlement after investing at `R_USD` |
| `MMH_RATE` | `=USD_MMH/FC_AMT` | Effective locked rate (must equal `F_CIP`) |

### 4.5 Put option

| Named range | Formula | Meaning |
|---|---|---|
| `PUT_PREM_T0` | `=FC_AMT*P_PUT` | Premium paid today (USD) |
| `PUT_PREM_FV` | `=PUT_PREM_T0*GROW_USD` | Premium carried to settlement at `R_USD` (opportunity cost), so all strategies compare at the same date |
| `PUT_FLOOR` | `=FC_AMT*K_PUT-PUT_PREM_FV` | Worst-case USD received with the put |
| `PUT_FLOOR_RATE` | `=PUT_FLOOR/FC_AMT` | Effective floor rate |
| `BE_PUT_VS_UNH` | `=K_PUT-P_PUT*GROW_USD` | Settlement spot below which the put beats staying unhedged |
| `BE_PUT_VS_FWD` | `=F0_in+P_PUT*GROW_USD` | Settlement spot above which the put beats the forward |

Put proceeds at any settlement spot `S_T`: `=FC_AMT*MAX(S_T,K_PUT)-PUT_PREM_FV`

### 4.6 Put–call parity (validation input)

| Named range | Formula | Meaning |
|---|---|---|
| `F_PCP` | `=K_CALL+(P_CALL-P_PUT)*GROW_USD` | Forward implied by the given premiums (valid because `K_PUT = K_CALL`) |

## 5. Scenario grid (tab `Scenarios`)

One row per settlement rate. The shock column is an input (blue); everything else is a formula.

| Column | Header | Formula (row r) |
|---|---|---|
| A | Shock vs spot | −10%, −7.5%, −5%, −2.5%, 0%, +2.5%, +5%, +7.5%, +10% |
| B | `S_T` (USD/EUR) | `=S0_in*(1+A_r)` |
| C | Unhedged | `=FC_AMT*B_r` |
| D | Forward | `=USD_FWD` |
| E | Money-market | `=USD_MMH` |
| F | Put | `=FC_AMT*MAX(B_r,K_PUT)-PUT_PREM_FV` |
| G–J | vs budget (each strategy) | `=C_r-BUDGET_USD`, `=D_r-BUDGET_USD`, etc. |
| K | Best strategy | `=INDEX($C$hdr:$F$hdr, MATCH(MAX(C_r:F_r), C_r:F_r, 0))` |

Below the grid, four **key rows** use the same column formulas with `S_T` set to: `K_PUT`, `BE_PUT_VS_UNH`, `F0_in`, `BE_PUT_VS_FWD`.

A line chart plots columns C–F against `S_T` (x-axis), with a horizontal reference line at `BUDGET_USD`.

## 6. Summary (tab `Summary`)

| Strategy | USD received | Certainty | Up-front cost | Keeps upside? |
|---|---|---|---|---|
| Unhedged | Depends on `S_T` (grid range shown) | None | 0 | Yes, all of it |
| Forward | `USD_FWD` | Locked | 0 | No |
| Money-market | `USD_MMH` | Locked | Uses borrowing line (`MMH_EUR_BORROW`) | No |
| Put | ≥ `PUT_FLOOR` | Floor only | `PUT_PREM_T0` | Yes, above `K_PUT` + premium |

Plus one line each: `FWD_GAP` (USD/EUR and $ total = `FWD_GAP*FC_AMT`) and overall check status from `Checks`.

## 7. Validation checks (tab `Checks`)

Each check returns **PASS**, **WARN** (model is correct, but an input looks inconsistent and needs a comment), or **FAIL** (model is wrong).

| ID | Check | Rule | Fail level | Expected at Stage 2 |
|---|---|---|---|---|
| C01 | Inputs complete | `COUNTBLANK` of all input cells = 0 | FAIL | PASS |
| C02 | Fixed terms unchanged | `FC_AMT=2100000` and `T_DAYS=91` | FAIL | PASS |
| C03 | Plausible units | FX inputs in [0.80, 1.60]; rates in [−0.01, 0.15]; premiums in (0, 0.10) | FAIL | PASS |
| C04 | Strikes at spot | `K_PUT=K_CALL` and `ABS(K_PUT/S0_in-1)<=1%` | WARN | PASS |
| C05 | MMH = CIP identity | `ABS(MMH_RATE-F_CIP)<1E-9` | FAIL | PASS |
| C06 | Quoted forward vs CIP | `ABS(FWD_GAP_PCT)<=0.5%` | WARN | **WARN** (+3.94%) |
| C07a | Parity vs CIP | `ABS(F_PCP/F_CIP-1)<=0.5%` | WARN | PASS (+0.12%) |
| C07b | Parity vs quoted forward | `ABS(F_PCP/F0_in-1)<=0.5%` | WARN | **WARN** (−3.68%) |
| C08 | Forward is flat | `MAX(D)-MIN(D)=0` on the grid | FAIL | PASS |
| C09 | Put floor holds | `MIN(F)` on the grid = `PUT_FLOOR` (±$1) | FAIL | PASS |
| C10 | Put costs premium above strike | Every row with `S_T>=K_PUT`: `C-F = PUT_PREM_FV` (±$1) | FAIL | PASS |
| C11 | Direction of risk | Unhedged rises with `S_T` (first row < last row) | FAIL | PASS |
| C12 | Break-even consistency | Put at `S_T=BE_PUT_VS_FWD` equals `USD_FWD` (±$1); put at `S_T=BE_PUT_VS_UNH` equals unhedged (±$1) | FAIL | PASS |
| C13 | Hand-check match | Model values equal Section 8 (±$1, ±0.000001 for rates) | FAIL | PASS |
| C00 | Overall | FAIL if any FAIL; else WARN if any WARN; else PASS | — | **WARN** |

The two expected WARNs are the point of the checks, not a defect: the given indicative forward (1.1653) sits about 442 pips above both the CIP forward (1.1211) and the forward implied by the given option premiums (1.1225). Stage 4 replaces `F0_in` and comments on this gap.

## 8. Hand-check values (for the Stage 3 audit)

Computed independently from the inputs in Section 3. The built workbook must reproduce these.

| Item | Expected |
|---|---|
| `T_YRS` | 0.252778 |
| `GROW_USD` / `GROW_FC` | 1.010111 / 1.006618 |
| `BUDGET_USD` | $2,346,120.00 |
| `F_CIP` | 1.121077 |
| `FWD_GAP` / `FWD_GAP_PCT` | +0.044223 / +3.94% (≈ $92,868 on the full amount) |
| `USD_FWD` | $2,447,130.00 |
| `MMH_EUR_BORROW` | €2,086,194.15 |
| `MMH_USD_T0` | $2,330,696.10 |
| `USD_MMH` | $2,354,262.03 |
| `PUT_PREM_T0` / `PUT_PREM_FV` | $30,870.00 / $31,182.13 |
| `PUT_FLOOR` / `PUT_FLOOR_RATE` | $2,314,937.87 / 1.102351 |
| `BE_PUT_VS_UNH` | 1.102351 |
| `BE_PUT_VS_FWD` | 1.180149 |
| `F_PCP` | 1.122453 |

Scenario grid (USD received at settlement):

| Shock | `S_T` | Unhedged | Forward | Money-market | Put | Best |
|---|---|---|---|---|---|---|
| −10.0% | 1.005480 | 2,111,508.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | Forward |
| −7.5% | 1.033410 | 2,170,161.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | Forward |
| −5.0% | 1.061340 | 2,228,814.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | Forward |
| −2.5% | 1.089270 | 2,287,467.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | Forward |
| 0.0% | 1.117200 | 2,346,120.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | Forward |
| +2.5% | 1.145130 | 2,404,773.00 | 2,447,130.00 | 2,354,262.03 | 2,373,590.87 | Forward |
| +5.0% | 1.173060 | 2,463,426.00 | 2,447,130.00 | 2,354,262.03 | 2,432,243.87 | Unhedged |
| +7.5% | 1.200990 | 2,522,079.00 | 2,447,130.00 | 2,354,262.03 | 2,490,896.87 | Unhedged |
| +10.0% | 1.228920 | 2,580,732.00 | 2,447,130.00 | 2,354,262.03 | 2,549,549.87 | Unhedged |

## 9. Build rules (for the AI in Stage 3)

1. Use exactly the tab names and named ranges above; workbook scope.
2. Inputs only on `Inputs` (blue font). Every other cell is a formula (black font). No typed numbers in `Calc`, `Scenarios`, `Summary`, or `Checks` except the shock column and check tolerances, which are labeled.
3. Formulas reference named ranges, not raw cell addresses, wherever a named range exists.
4. Formats: FX rates 4–6 decimals; USD `$#,##0.00`; EUR `€#,##0.00`; rates as `0.000%`.
5. No macros, no external links, no volatile functions (`OFFSET`, `INDIRECT`, `NOW`).
6. `Checks` uses conditional formatting: green PASS, amber WARN, red FAIL.
7. Do not round inside formulas; round only in display formats.

## 10. Assumptions and limitations

- **Day count:** both rates use ACT/360 with simple interest over 91 days. The H.15 Treasury yield is quoted on a bond-equivalent basis, so this slightly overstates USD interest; Stage 4 can switch to a USD money-market rate (e.g., SOFR-based) if precision matters.
- **Rates are risk-free proxies.** The firm's real borrowing rate (for the MMH) and deposit rate are higher and lower respectively; the MMH result is therefore a best case.
- **Spot is a reference fixing** (ECB, 14:15 CET), not a dealer bid. Real execution would sell EUR at the bid.
- **Premiums are given, not priced.** No volatility model is built; parity is used only as a consistency check.
- **No credit, margin, or transaction costs,** and the customer is assumed to pay in full and on time (no volume risk).
- **The indicative forward is a teaching placeholder** that is inconsistent with both CIP and the given premiums. Until Stage 4 it flatters the forward hedge by roughly $93,000.

## 11. What changes at Stage 4

Replace `S0_in`, `R_USD`, `R_FC` with sourced, timestamped live values, and `F0_in` with a live forward or `F_CIP`. Update each input's Source and As-of columns, re-run `Checks`, and record the before/after in `data/YYYY-MM-DD-craft-whiskey-market-data-memo.md`. `FC_AMT`, `T_DAYS`, `P_PUT`, and `P_CALL` do not change.

## Sources

- ECB euro foreign exchange reference rates (USD, 30 Oct 2026): https://data.ecb.europa.eu/key-figures/ecb-interest-rates-and-exchange-rates/exchange-rates
- Federal Reserve H.15 Selected Interest Rates (released 30 Oct 2026): https://www.federalreserve.gov/releases/h15/
- 3-month Euribor fixing (28 Oct 2026): https://euribor-rates.info/en/euribor-rate/3-month-rate/

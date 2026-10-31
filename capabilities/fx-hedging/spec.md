<div style="border-top: 6px solid #024731; border-bottom: 1px solid #B2B2B2; padding: 12px 0; margin-bottom: 24px; font-family: 'Open Sans', Helvetica, Arial, sans-serif;">
  <div style="color: #024731; font-weight: 700; letter-spacing: 0.06em; text-transform: uppercase; font-size: 0.85rem;">University of Hawaiʻi at Mānoa · Shidler College of Business</div>
  <div style="color: #000000; font-weight: 700; font-size: 1.25rem; margin-top: 4px;">FIN 321: International Finance and Securities</div>
  <div style="color: #525252; font-weight: 400; font-size: 0.95rem;">FX Transaction Hedging Project — Technical Specification</div>
</div>

<!--
BRAND FORMATTING — applied per docs/_branding/design.json (v1.0.0)
  ┌─ Colors ─────────────────────────────────────────────────────────────┐
  │ Primary green ........... #024731  RGB 2,71,49    Pantone 3435 C     │
  │ Primary black ........... #000000  RGB 0,0,0      Process Black      │
  │ Silver (secondary) ...... #B2B2B2  RGB 178,178,178 Cool Gray 5 C     │
  │ White (secondary) ....... #FFFFFF                                    │
  │ Neutral-600 (muted) ..... #525252  (secondary text, captions)        │
  │ UH-green 700 (hover) .... #013D26  (link hover, pressed state)       │
  │ UH-green 50 (tint) ...... #E6F2EF  (callout backgrounds)             │
  │ Yellow (Excel only) ..... #FFFF00  (input highlight — not brand)     │
  ├─ Typography ─────────────────────────────────────────────────────────┤
  │ Headings (web)  .......... Open Sans Bold (H1/H2) / Semibold (H3/H4) │
  │ Headings (print)  ........ Avenir Bold                               │
  │ Body (web)  .............. Open Sans Regular                         │
  │ Body (print)  ............ Avenir Book                               │
  │ Fallback stack  .......... Helvetica, Arial, sans-serif              │
  │ Monospace  ............... Consolas / ui-monospace                   │
  │ Body minimum size  ....... 10 pt (11–12 pt preferred for print)      │
  │ Leading  ................. 3–5 pt greater than type size (print)     │
  │ Alignment  ............... Flush left, ragged right                  │
  ├─ Accessibility ──────────────────────────────────────────────────────┤
  │ • ADA-compliant contrast ratios for ALL text and UI elements         │
  │ • No red body type                                                   │
  │ • No layouts that are too dark for readability                       │
  │ • No custom palettes or gradients outside the official brand         │
  └──────────────────────────────────────────────────────────────────────┘
  Full brand standard: docs/_branding/design.json · Source: https://manoa.hawaii.edu/brand/
-->


# U.S. Craft-Whiskey Distiller — FX Transaction Hedge Model · Technical Specification

> <span style="color:#024731; font-weight:700;">Technical specification</span> for the FX transaction hedge model — the named-range contract, calculation flow, and validation checks, precise enough that an AI or a colleague could build (or rebuild) the workbook from this document alone. This spec is the input the AI-assisted build works from.

| Field | Value |
|------|------|
| **Created by** | Alexis Fuchs |
| **Updated by** | Alexis Fuchs |
| **Date Created** | 2026-10-30 |
| **Date Updated** | 2026-10-30 |
| **Version** | 1.0 |
| **LLM Used** (optional) | Claude — drafted v0.1 and v1.0 from the Stage 2 brief and this template; reviewed and edited by the author (see Appendix A and `prompt-log.md`) |
| **Role** | Treasury Analyst |
| **Audience** | CFO |
| **Companion Workbook** | `capabilities/fx-hedging/model.xlsx` (built in Stage 3) |

---

## 1. Problem Statement

The firm, a U.S. craft-whiskey distiller with USD as its functional currency, holds a **EUR 2,100,000 receivable** from a French luxury-hotel group, settling in **91 days (2027-01-29, counting from 2026-10-30)**. Rates are quoted as **USD per EUR**. A fall in EURUSD over the 91 days reduces realized USD proceeds one-for-one: each 1% fall costs about **$23,461**, and a 5% fall costs **$117,306** against the $2,346,120 the receivable is worth at today's spot. The objective is to protect the budgeted USD value at the lowest cost and with the least upside given up. This specification documents the workbook that compares four strategies — **no hedge**, **forward hedge**, **money-market hedge**, and **option (put) hedge** — and produces the sensitivity evidence for the Stage 5 decision memo to the CFO.

---

## 2. Inputs (Known Variables)

All inputs are workbook-scope **named ranges** on the `Inputs` tab. Market values below are **placeholders — "indicative — replaced with live market data at Stage 4."** Sources and access timestamps are recorded on the `Notes` tab.

### 2.1 Core Inputs

| Standardized Name | Description | Unit | Placeholder value | Placeholder source (date) | Stage 4 source |
|-------------------|-------------|------|------------------:|---------------------------|----------------|
| `FC_AMT` | EUR receivable notional | EUR | 2,100,000 | Scenario 10 (fixed) | **Fixed — never changes** |
| `T_DAYS` | Days to settlement | Days | 91 | Scenario 10 (fixed) | **Fixed — never changes** |
| `S0_in` | Spot rate at inception | USD per EUR | 1.1172 | ECB euro reference rate (2026-10-30) | Live EURUSD spot, sourced and timestamped |
| `F0_in` | 91-day forward rate | USD per EUR | 1.1653 | Scenario 10 indicative forward (set 2026-10-18 for teaching) | Live 3-month forward quote, or CIP-implied `S0_in × DF_USD / DF_FC`; comment on the gap |
| `R_USD` | USD interest rate to settlement | Annual % | 4.000% | Fed H.15, 3-month Treasury constant maturity (2026-10-29) | Live 3-month USD rate (H.15 or Term SOFR) |
| `R_FC` | EUR interest rate to settlement | Annual % | 2.618% | 3-month Euribor fixing, EMMI (2026-10-28) | Live 3-month Euribor fixing |
| `BASIS` | Day-count denominator | Days | 360 | ACT/360 money-market convention (USD and EUR) | Unchanged |
| `K_PUT` | Put strike (at the money) | USD per EUR | 1.1172 | Scenario 10: strike at spot (= `S0_in`) | Re-set at or near the Stage 4 spot |
| `K_CALL` | Call strike (at the money) | USD per EUR | 1.1172 | Scenario 10: strike at spot (= `S0_in`) | Re-set at or near the Stage 4 spot |
| `PREM_PUT` | Put premium per 1 EUR | USD | 0.0147 | Scenario 10 (given) | **Kept** — retail quotes unreliable |
| `PREM_CALL` | Call premium per 1 EUR | USD | 0.0199 | Scenario 10 (given) | **Kept** — used only for the parity check |
| `STEP_FRAC` | Sensitivity grid step | % of `S0_in` | 1.00% | Stage 2 brief: ±5% in 1% steps | Unchanged |

### 2.2 Derived / Intermediate Values

| Name | Description | Source |
|------|-------------|--------|
| `DF_USD` | USD accumulation factor to settlement | `1 + R_USD × T_DAYS / BASIS` |
| `DF_FC` | EUR accumulation factor to settlement | `1 + R_FC × T_DAYS / BASIS` |
| `FV_PREM_PUT` | Future value of put premium at settlement (negative) | `−PREM_PUT × FC_AMT × DF_USD` |
| `FV_PREM_CALL` | Future value of call premium at settlement (negative) | `−PREM_CALL × FC_AMT × DF_USD` |
| `F_CIP` | Forward implied by covered interest parity | `S0_in × DF_USD / DF_FC` |
| `F_PCP` | Forward implied by put–call parity (valid because `K_PUT = K_CALL`) | `K_CALL + (PREM_CALL − PREM_PUT) × DF_USD` |
| `S_T_grid` | Sensitivity spot grid at settlement | `S0_in × (1 + k × STEP_FRAC)`, `k = −5 … +5` |
| `USD_NO_HEDGE` | USD proceeds under no hedge | `S_T × FC_AMT` |

### 2.3 Tab Architecture & Color Legend

| Tab | Purpose |
|-----|---------|
| `Cover` | Scenario (Scenario 10, craft-whiskey), author, version, date, and the data-source documentation for every market input |
| `Legend` | The color convention below, with one sample cell of each |
| `Inputs` | The twelve §2.1 named ranges — the only cells an analyst edits |
| `Forward` | Step 2: `USD_FWD` |
| `MoneyMarket` | Step 3: borrow, convert, invest; `USD_MM`; parity check vs. forward |
| `Options` | Step 4: premiums, `FV_PREM_PUT`, put payoff, `USD_FLOOR_PUT`, put–call parity |
| `Sensitivity` | Strategy summary, the 11-row grid (`tbl_Sensitivity`), hedge-profit columns, winner labels, and `chart_Sensitivity` |
| `Checks` | The §5.2 validation rules with PASS / FLAG results |
| `Notes` | Assumptions (§3), source URLs and access timestamps, and the change log |

**Color convention** (project standard): **yellow fill** = inputs · **blue font** = assumptions (`BASIS`, `STEP_FRAC`) · **green fill** = formulas · **gray fill** = named outputs.

---

## 3. Assumptions & Constraints

- **Quote convention:** all rates are **USD per EUR**; a higher quote means a stronger euro.
- **Horizon:** single maturity, `T_DAYS = 91`; no rolling or partial hedges.
- **Day-count basis:** simple interest, `r × T_DAYS / BASIS`, with one `BASIS = 360` (ACT/360) for both legs. The H.15 Treasury rate is quoted on a bond-equivalent basis, so this slightly overstates USD interest; see §6.2.
- **Parity:** the money-market hedge is expected to replicate the forward under covered interest parity. A gap tests the quoted forward; it is not a model error.
- **Option premium:** paid up front in USD, quoted per 1 EUR, no contract multiplier. Recorded as a **negative** cash flow and carried to settlement at `R_USD` so it compares with settlement-date proceeds.
- **Excluded:** counterparty and credit risk, transaction costs and bid-ask spreads, taxes and hedge accounting, and customer late payment or non-payment.
- **Scenario construction:** `S_T` varies deterministically across the grid; no probabilities or volatility model.

---

## 4. Calculation Flow

Named-range notation only — never cell addresses. Formulas are for a **receivable**.

### Step 1 — Derived inputs

1. `DF_USD = 1 + R_USD × T_DAYS / BASIS`
2. `DF_FC = 1 + R_FC × T_DAYS / BASIS`
3. `FV_PREM_PUT = −PREM_PUT × FC_AMT × DF_USD`
4. `FV_PREM_CALL = −PREM_CALL × FC_AMT × DF_USD`

### Step 2 — Forward hedge (certainty benchmark)

- `USD_FWD = FC_AMT × F0_in` — locked at t₀; constant across the `S_T` grid.

### Step 3 — Money-market hedge (parity check)

1. Borrow `MM_FC_BORROW = FC_AMT / DF_FC` EUR today.
2. Convert at spot: `MM_USD_T0 = MM_FC_BORROW × S0_in`.
3. Invest to settlement: `USD_MM = MM_USD_T0 × DF_USD`.
4. At settlement the EUR receivable repays the EUR loan exactly; `USD_MM` is locked.

> **Parity check:** `PARITY_GAP = USD_FWD / USD_MM − 1` must be within ±0.05%. Equivalently, `F0_in ≈ F_CIP`.

### Step 4 — Option hedge (floor with upside)

- Pay `PREM_PUT × FC_AMT` USD today for a put on EUR struck at `K_PUT`.
- For each `S_T`: `USD_PUT(S_T) = MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`.
- **Put floor:** when `S_T ≤ K_PUT`, the put pays `(K_PUT − S_T) × FC_AMT`, so proceeds are fixed at `K_PUT × FC_AMT + FV_PREM_PUT`.
- **Upside participation:** when `S_T > K_PUT`, the put expires unused and proceeds rise with `S_T`, less the premium: `S_T × FC_AMT + FV_PREM_PUT`.
- **Call (parity check only):** a long call does not hedge a receivable. Its payoff, `MAX(0, S_T − K_CALL) × FC_AMT`, is used only through `F_PCP`.

### Step 5 — Sensitivity table (`tbl_Sensitivity`, one row per `S_T` in `S_T_grid`)

| Column | Output | Formula |
|--------|--------|---------|
| Shock | `k × STEP_FRAC` | −5% … +5% |
| `S_T` | Settlement spot | `S0_in × (1 + k × STEP_FRAC)` |
| No hedge | `USD_NO_HEDGE(S_T)` | `S_T × FC_AMT` |
| Forward | `USD_FWD` | constant |
| Money market | `USD_MM` | constant |
| Option (put) | `USD_PUT(S_T)` | `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT` |
| Hedge profit (3 columns) | `HEDGE_PROFIT_k(S_T)` | `USD_k − USD_NO_HEDGE` for forward, money market, option |
| Overall winner | label | `ARGMAX(USD_NO_HEDGE, USD_FWD, USD_MM, USD_PUT)` via `INDEX/MATCH` |
| Best active hedge | label | `ARGMAX(USD_FWD, USD_MM, USD_PUT)` via `INDEX/MATCH` |

### Step 6 — Summary metrics (scalar outputs)

- `USD_FLOOR_PUT = MIN(USD_PUT)` across `S_T_grid`.
- `USD_BASE_k` = `USD_k` at `S_T = S0_in` for each strategy (feeds §5.1).

### Step 7 — Payable variant

Not applicable: the exposure is a receivable. `K_CALL` and `PREM_CALL` are kept only for the put–call parity check.

---

## 5. Outputs

Every output is a **gray** named cell or named table.

| Output name | Description | Tab | Purpose |
|-------------|-------------|-----|---------|
| `USD_FWD`, `USD_MM` | Locked proceeds, forward and money market | `Forward`, `MoneyMarket` | Certainty benchmarks |
| `USD_FLOOR_PUT` | Worst-case put proceeds on the grid | `Options` | Downside protection |
| `USD_BASE_NOHEDGE`, `USD_BASE_FWD`, `USD_BASE_MM`, `USD_BASE_PUT` | Proceeds at `S_T = S0_in` | `Sensitivity` | Base case (§5.1) |
| `PARITY_GAP`, `F_CIP`, `F_PCP` | Forward vs. parity diagnostics | `MoneyMarket`, `Options` | Tests the quoted forward |
| `tbl_Summary` | The four strategies: locked/floor proceeds, up-front cost, upside kept | `Sensitivity` (above grid) | Executive at-a-glance |
| `tbl_Sensitivity` | §4 Step 5 grid, 11 rows | `Sensitivity` | Core evidence |
| `chart_Sensitivity` | §7 line chart | `Sensitivity` | Visual comparison |
| `tbl_Checks` | §5.2 rules with PASS / FLAG | `Checks` | Audit trail |

### 5.1 Computed Base-Case Values

At `S_T = S0_in = 1.1172` with the §2.1 placeholders. Regression checkpoint for Stages 3 and 4.

| Strategy | USD Proceeds (Receivable) | USD Outlay (Payable) | Hedge Profit vs. No Hedge |
|----------|--------------------------:|---------------------:|--------------------------:|
| No hedge | 2,346,120.00 | n/a | — |
| Forward | 2,447,130.00 | n/a | +101,010.00 |
| Money market | 2,354,262.03 | n/a | +8,142.03 |
| Option (put) | 2,314,937.87 | n/a | −31,182.13 |

Intermediate check values: `DF_USD` 1.010111 · `DF_FC` 1.006618 · `MM_FC_BORROW` €2,086,194.15 · `MM_USD_T0` $2,330,696.10 · `FV_PREM_PUT` −$31,182.13 · `F_CIP` 1.121077 · `F_PCP` 1.122453 · `PARITY_GAP` +3.94% ($92,867.97).

### 5.2 Validation Rules (Check Figures)

These become the Stage 3 audit checklist. Results show on `Checks` as **PASS** or **FLAG**.

| ID | Rule | Check figure | Expected at Stage 2 |
|----|------|--------------|---------------------|
| V1 | Forward ≈ money-market parity: `ABS(PARITY_GAP) ≤ 0.05%` | 0.05% | **FLAG** (+3.94%) — placeholder `F0_in`; resolved at Stage 4 |
| V2 | Money market equals CIP forward: `USD_MM / FC_AMT = F_CIP` | difference < 1E-9 | PASS |
| V3 | Put kink: `USD_PUT(K_PUT) = K_PUT × FC_AMT + FV_PREM_PUT` | $2,314,937.87 | PASS |
| V4 | Put floor holds: `MIN(USD_PUT) = USD_FLOOR_PUT` | $2,314,937.87 | PASS |
| V5 | Option proceeds vary continuously: adjacent-row change ≤ `STEP_FRAC × S0_in × FC_AMT` | $23,461.20 | PASS |
| V6 | Forward and money market constant across rows: `MAX − MIN = 0` | 0 | PASS |
| V7 | Put–call parity vs. CIP: `ABS(F_PCP / F_CIP − 1) ≤ 0.5%` | 0.5% | PASS (+0.12%) |
| V8 | Put–call parity vs. quoted forward: `ABS(F_PCP / F0_in − 1) ≤ 0.5%` | 0.5% | **FLAG** (−3.68%) — same placeholder |
| V9 | No error cells: `SUMPRODUCT(--ISERROR(range)) = 0` on every tab | 0 | PASS |
| V10 | Every green and gray cell is a formula: `ISFORMULA` = TRUE | all TRUE | PASS |
| V11 | Fixed terms unchanged: `FC_AMT = 2,100,000`, `T_DAYS = 91` | exact | PASS |
| V12 | Base case reproduces §5.1 | ±$0.01 | PASS |

Both FLAGs point to the same thing: the indicative `F0_in` (1.1653) sits about 442 pips above both `F_CIP` (1.1211) and `F_PCP` (1.1225), and it flatters the forward by about $92,868. They are expected, documented, and cleared at Stage 4 — not model errors.

---

## 6. Model Review — What Worked & What to Improve

Written before the build, as a known-risks register.

### 6.1 What Worked

- Two independent parity checks (V1, V8) already catch that the placeholder forward is out of line with the market, so the Stage 5 recommendation cannot be driven by a stale quote.
- The base case and the full grid (§7.1) were computed independently of the workbook, so the Stage 3 audit is a comparison, not a rebuild.

### 6.2 What to Improve (improvement brief for Stage 3)

- **Forward input:** replace `F0_in` at Stage 4 with a live quote or `F_CIP`, and record the before/after gap.
- **Day count:** split `BASIS` into `BASIS_USD` and `BASIS_FC` (both 360 for USD/EUR), and consider Term SOFR instead of the bond-equivalent H.15 rate.
- **Costs:** add a bid-ask spread sensitivity on `S0_in` and `F0_in`.
- **Default priority for the build:** every §2 name as a workbook named range, plus `chart_Sensitivity` exactly as §7 specifies.

### 6.3 Auditability Checklist

- [ ] Every input has a standardized named range from §2.1
- [ ] Every formula in §4 uses named ranges — no bare `$F$n` references
- [ ] Money-market hedge ties to forward hedge within 0.05% (parity check; expected FLAG until Stage 4)
- [ ] Put payoff at `S_T = K_PUT` equals `K_PUT × FC_AMT + FV_PREM_PUT` (kink verification)
- [ ] Sensitivity grid is symmetric around `S_T = S0_in` and driven by `STEP_FRAC`
- [ ] `Notes` tab records spot / forward / rate sources with access dates
- [ ] Cell colors match the legend: yellow inputs, blue assumptions, green formulas, gray outputs

---

## 7. Sensitivity Plan

- **Grid:** `S_T_grid` spans `S0_in × (1 ± 5%)` in 1% steps (`STEP_FRAC`) — 11 rows, including the baseline.
- **Strategies plotted:** no hedge, forward, money market, option (put).
- **Primary chart (`chart_Sensitivity`):** line chart, `S_T` on the x-axis and USD proceeds on the y-axis, one series per strategy, plus a horizontal reference line at the budget (`FC_AMT × S0_in` = $2,346,120). Forward and money market are flat; no hedge is a straight upward line; the put is flat below `K_PUT` and rises above it.
- **Secondary table:** the three hedge-profit columns.
- **What the CFO should see:** how much each hedge protects if the euro falls, what each gives up if it rises, where the lines cross, and the cost of certainty versus the premium for optionality.

### 7.1 Expected Grid (placeholder inputs)

| Shock | `S_T` | No hedge | Forward | Money market | Option (put) | Profit: Fwd | Profit: MM | Profit: Put | Overall winner | Best active hedge |
|---|---|---:|---:|---:|---:|---:|---:|---:|---|---|
| −5% | 1.061340 | 2,228,814.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +218,316.00 | +125,448.03 | +86,123.87 | Forward | Forward |
| −4% | 1.072512 | 2,252,275.20 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +194,854.80 | +101,986.83 | +62,662.67 | Forward | Forward |
| −3% | 1.083684 | 2,275,736.40 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +171,393.60 | +78,525.63 | +39,201.47 | Forward | Forward |
| −2% | 1.094856 | 2,299,197.60 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +147,932.40 | +55,064.43 | +15,740.27 | Forward | Forward |
| −1% | 1.106028 | 2,322,658.80 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +124,471.20 | +31,603.23 | −7,720.93 | Forward | Forward |
| 0% | 1.117200 | 2,346,120.00 | 2,447,130.00 | 2,354,262.03 | 2,314,937.87 | +101,010.00 | +8,142.03 | −31,182.13 | Forward | Forward |
| +1% | 1.128372 | 2,369,581.20 | 2,447,130.00 | 2,354,262.03 | 2,338,399.07 | +77,548.80 | −15,319.17 | −31,182.13 | Forward | Forward |
| +2% | 1.139544 | 2,393,042.40 | 2,447,130.00 | 2,354,262.03 | 2,361,860.27 | +54,087.60 | −38,780.37 | −31,182.13 | Forward | Forward |
| +3% | 1.150716 | 2,416,503.60 | 2,447,130.00 | 2,354,262.03 | 2,385,321.47 | +30,626.40 | −62,241.57 | −31,182.13 | Forward | Forward |
| +4% | 1.161888 | 2,439,964.80 | 2,447,130.00 | 2,354,262.03 | 2,408,782.67 | +7,165.20 | −85,702.77 | −31,182.13 | Forward | Forward |
| +5% | 1.173060 | 2,463,426.00 | 2,447,130.00 | 2,354,262.03 | 2,432,243.87 | −16,296.00 | −109,163.97 | −31,182.13 | No hedge | Forward |

---

## 8. Limitations & Next Steps

**Limitations.** This specification does not incorporate:
- Partial, layered, or dynamic hedging (static, full-notional hedge at t₀)
- Credit, counterparty, and settlement risk, or customer late payment
- Implied-volatility option pricing (premia are scenario inputs)
- Hedge accounting (ASC 815 / IFRS 9)

**Next steps.** (a) Stage 3: hand §2–§7 to an AI as the build prompt, then audit `model.xlsx` against §5.1, §5.2, and §7.1; (b) Stage 4: replace the four market placeholders with sourced live data and clear the V1/V8 FLAGs or explain them; (c) Stage 5: validate against an independent LLM run and write the decision memo.

---

## 9. How This Sets Up Stages 3–5

| What's Written in Stage 2 | What It Enables in Stages 3–5 |
|---------------------------|----------------------------|
| Standardized named ranges (§2) | The AI uses the course names; no improvisation |
| Step-by-step calculation flow (§4) | Correct, auditable hedge formulas |
| Improvement brief (§6.2) | The AI builds the improved version |
| Output names, chart spec, validation rules (§5, §7) | The exact tables, chart, and checks the memo needs |
| Base-case values and expected grid (§5.1, §7.1) | Regression checkpoints for Stages 3 and 4 |

---

## Appendix A — Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 0.1 | 2026-10-30 | Alexis Fuchs (Claude-drafted) | Initial draft, written before the course template was found |
| 1.0 | 2026-10-30 | Alexis Fuchs (Claude-drafted, author-reviewed) | Review against the Stage 2 brief found five gaps; rebuilt on `template-spec.md`: (1) premium names `P_PUT`/`P_CALL` → `PREM_PUT`/`PREM_CALL`; (2) tabs changed to Cover, Legend, Inputs, Forward, MoneyMarket, Options, Sensitivity, Checks, Notes; (3) sensitivity ±10% in 2.5% steps → ±5% in 1% steps (`STEP_FRAC`); (4) color code → yellow / blue / green / gray; (5) derived names aligned to the template (`GROW_USD` → `DF_USD`, `USD_MMH` → `USD_MM`, put premium as signed `FV_PREM_PUT`) |

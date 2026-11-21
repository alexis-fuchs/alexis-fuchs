---
type: memo
title: "Market-Data Memo: Live Inputs for the Craft-Whiskey Hedge Model"
scenario: craft-whiskey
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-20
status: committed          # committed | superseded
llm_used: "Claude"
---

# Market-Data Memo: Live Inputs for the Craft-Whiskey Hedge Model

**To:** CFO  
**From:** Alexis Fuchs, Treasury Analyst  
**Date:** 2026-11-20 (market data as of the 20 November 2026 close)  
**Version:** 1.0  
**LLM Used:** Claude (searched for and cross-checked the market data, computed the figures independently, populated the workbook, and drafted this memo; reviewed by the author)  
**Workbook:** [`capabilities/fx-hedging/model.xlsx`](../capabilities/fx-hedging/model.xlsx) v1.6 · **Spec:** [`capabilities/fx-hedging/spec.md`](../capabilities/fx-hedging/spec.md) v1.6

---

## Summary

I replaced the placeholder spot and interest rates with live data from the 20 November 2026 close and entered them only through the workbook's named input cells. No formula broke, and every output matches an independent recalculation. Populating the workbook did expose four structural problems, all fixed and recorded below (§4). The workbook now shows **11 PASS and 2 FLAG**.

The course's FX Hedging Lab reproduces the forward and money-market results to the dollar. Its put is $337.10 higher in every row, because it does not carry the premium to settlement; that is a convention difference, explained in §7.

Both FLAGs are about the bank's forward quote, not the model. **The quote, 1.1653, sits 622 pips (+5.64%, $130,698 on the receivable) above the forward implied by today's spot and interest rates (1.1031)**, and 640 pips above the live market forward (1.1013). At Stage 2 the gap was 442 pips ($92,868). It persisted and widened because the euro fell 1.6% while the quote stayed put. Under the course rule I kept the quote as `F0_in`, recorded the gap, and asked the bank for a requote (§3).

---

## 1. Inputs and provenance

All values below were retrieved on **2026-11-20 at about 22:30 HST (2026-11-21 08:30 UTC)**. Full URLs are in §8 and on the workbook's `Notes` tab.

| Input | Value | Unit | Source (instrument) | Data timestamp | Proxy, computation or note |
|---|---:|---|---|---|---|
| `S0_in` | **1.0990** | USD per EUR | ECB euro reference rate, US dollar | 2026-11-20, 14:15 CET concertation (published about 16:00 CET) | Official daily reference fix, chosen because an auditor can re-pull it exactly. By the New York close the market was 21.7 pips lower (1.09683, FXEmpire, 21:59 UTC), about $4,557 on the receivable. |
| `R_USD` | **4.320%** | Annual, bond-equivalent | U.S. Treasury Daily Par Yield Curve, 3-month CMT (the series the Fed republishes as H.15 / FRED `DGS3MO`) | 2026-11-20 close | Tenor-matched to 91 days. The latest H.15 release (19 Nov, published 20 Nov) shows 4.34%; Treasury's own table already has the 20 Nov close. Basis note in §2. |
| `R_FC` | **2.847%** | Annual, ACT/360 | 3-month Euribor fixing (EMMI) | 2026-11-19, 11:00 CET fixing | The latest fixing posted on free sources at retrieval: euribor-rates.eu and euriborrates.com both show 19 Nov as the most recent fixing. |
| `F0_in` | **1.1653** | USD per EUR | **The bank's quote**: Scenario 10 forward, 91 days | Quoted 2026-10-18 | **Kept.** Never replaced by a computed rate (course rule). Compared with the CIP forward in §3; requote requested. |
| `K_PUT`, `K_CALL` | **1.0990** | USD per EUR | Scenario 10 convention: strike at spot | = live `S0_in`, 2026-11-20 | Re-set from 1.1172 to the live spot. |
| `PREM_PUT` | **0.0147** | USD per EUR | Scenario 10 (given) | 2026-10-18 | **Assumption:** kept as given, because retail-accessible FX option quotes are unreliable. |
| `PREM_CALL` | **0.0199** | USD per EUR | Scenario 10 (given) | 2026-10-18 | **Assumption:** kept as given (same reason). |
| `FC_AMT` | **2,100,000** | EUR | Scenario 10 | Fixed | Unchanged. |
| `T_DAYS` | **91** | Days | Scenario 10 | Fixed | Unchanged; counted from the pricing date, so priced 2026-11-20 and settling 2027-02-19. |
| `BASIS`, `STEP_FRAC` | 360 · 1% | — | Model assumptions | — | Unchanged. |

**Cross-check source, not an input:** live 3-month EUR/USD forward, FXEmpire forward-rates page, 2026-11-20 21:59 UTC. Spot 1.09683, 3-month forward mid **1.10127** (bid 1.10115 / ask 1.10138), forward points +44.36.

## 2. Why these rates

- **`R_USD`, the 3-month Treasury CMT.** It matches the 91-day horizon, which the course brief asks for ("at or near your scenario's tenor"). It is the risk-free rate the firm would earn on the dollars the money-market hedge invests, and it is an official, timestamped daily series anyone can re-pull. I rejected the 1-year CMT (4.51%: wrong tenor), effective fed funds (3.88%: overnight) and Term SOFR (licensed, not freely re-pullable).
- **`R_FC`, 3-month Euribor.** It matches the tenor, and it is the reference rate for *borrowing* euros, which is the first leg of the money-market hedge. It is also quoted on ACT/360, like the model. I rejected 12-month Euribor (3.351%: wrong tenor).
- **What the choice costs, in dollars:**
  - *Day-count basis.* The Treasury CMT is a bond-equivalent (≈ACT/365) yield, and the model applies ACT/360. Converting it to a money-market basis (4.261%) would lower `USD_MM` by **$342.77** and `F_CIP` by 1.6 pips. I kept the published value so an auditor can re-pull it exactly. The effect is under 0.3% of the quote gap.
  - *Credit mix.* Euribor carries bank credit risk and the T-bill does not, so this pair slightly understates the rate gap. The result: our CIP forward points (+40.6 pips) sit about 4 pips below the market's (+44.4), about $790 on the receivable. That small difference is itself the evidence that the rate choice is sound.
  - *Best case.* A real firm borrows euros above Euribor and invests dollars at or below the T-bill rate, so the model's money-market result is an upper bound.

## 3. The bank's quote vs. the CIP-implied forward

F_CIP = S0 × (1 + R_USD × 91/360) / (1 + R_FC × 91/360) = 1.0990 × 1.010920 / 1.007197 = **1.103063**

| Forward | Rate | Quote minus this (pips) | USD proceeds on EUR 2,100,000 | Quote's advantage (USD) |
|---|---:|---:|---:|---:|
| **Bank's quote** (`F0_in`) | 1.165300 | — | $2,447,130.00 | — |
| CIP forward from live rates (`F_CIP`) | 1.103063 | **+622.4** | $2,316,431.87 | **+$130,698.13 (+5.64%)** |
| Live market 3-month forward (FXEmpire mid) | 1.101270 | +640.3 | $2,312,667.00 | +$134,463.00 (+5.81%) |
| Put–call-parity forward (`F_PCP`) | 1.104257 | +610.4 | $2,318,939.25 | +$128,190.75 |

**Does the gap persist?** Yes, and it grew. At Stage 2, on placeholder data, the quote sat 442 pips ($92,867.97) above `F_CIP`. On live data it sits 622 pips ($130,698.13) above. Spot fell from 1.1172 to 1.0990 (−1.63%), and the quote did not move.

**What I'd tell the CFO.** Taken at face value, the quote beats the money-market hedge by about $131,000 and wins in every row of the sensitivity grid, even at +5%. No working market quotes that. The three independent forwards (CIP, put–call parity and the live market) agree within 30 pips of each other, around 1.101–1.104, and the quote is 600-plus pips away from all of them. If the quote were executable, selling euros forward at 1.1653 and covering through the money market would lock in a riskless profit of about $131,000. So I read it as a stale or mispriced quote, not an opportunity.

**What I'd want to see before believing it:**
1. A fresh quote from the bank, dated today, for the 2027-02-19 value date.
2. Forward points consistent with the rate differential: about +40–45 pips over spot, not +663.
3. Confirmation that the quote is executable for EUR 2,100,000.

**Action taken:**
- `F0_in` stays **1.1653**.
- V1 and V8 stay FLAG; they are reported here, not forced to pass.
- I asked the bank (the instructor) for a requote on 2026-11-20, in a comment on [pull request #2](https://github.com/alexis-fuchs/alexis-fuchs/pull/2). If a requote arrives, it goes into `F0_in` with its date, and this memo gets a new version.

## 4. Populating the workbook: what changed, what broke, what I fixed

**What I entered.** Five input cells changed, all through their named ranges: `S0_in`, `R_USD`, `R_FC`, `K_PUT` and `K_CALL`. Nothing else was touched for the first test. I loaded them into the committed v1.5 workbook first, unchanged otherwise. All 379 formulas recalculated without an error, and every named output and all 11 grid rows matched my independent calculation to the cent. That state is committed on its own, so the history shows what broke before anything was fixed.

**What broke, and the fixes.** Each one is recorded in spec v1.6 and its change log, and the workbook was regenerated from the spec.

1. **V12 FLAGged ($38,244.97 off).** Its check figures were typed from the Stage 2 placeholder base case, so any real data would fail it.
   - *Fix:* spec §5.1b adds the Stage 4 base case, computed independently (no hedge $2,307,900.00 · forward $2,447,130.00 · money market $2,316,431.87 · put $2,276,692.90). V12 now compares against the base case for the inputs in force and says to re-baseline when the inputs change. V12 now PASSes, $0.003 off.
2. **The provenance contradicted the data.** The Cover showed the live spot of 1.0990 next to "ECB … 2026-10-30", because source, as-of and status text were typed separately on `Inputs`, the Cover and `Notes`, and none of it updated.
   - *Fix:* the text is typed once, on `Inputs`, which now also has a retrieval-time column. The Cover reads it by formula.
3. **The workbook planned to replace the forward.** The `Inputs` tab said "replace `F0_in` with a live forward or the CIP forward", and a Checks note said V1 and V8 "clear at Stage 4". The instructor's review (PR #2) and the Stage 4 brief both say the opposite.
   - *Fix:* corrected in spec §2.1, §5.2, §6.2 and §8 and in the workbook text. The Checks note now computes the gap from the live values. I also added `QUOTE_GAP` (`F0_in − F_CIP`) so the gap shows as a rate as well as in USD.
4. **A defect I introduced, caught by the model's own check.** My first version of the Cover's new "Read first" line read the PASS/FLAG counts. Those counts depend on V9, and V9 scans the Cover, so this made a circular reference. V9 FLAGged it before anything shipped.
   - *Fix:* the line no longer reads the counts.

**Added to answer the instructor's two questions (PR #2):**
- **V13, a call kink test.** It prices the call one step below, at and one step above `K_CALL`. I proved it can fail by planting a put-shaped call payoff and a flipped premium sign: V13 FLAGged both.
- **A Cover "Read first" line pointing to V1.**

Workbook and spec both carry version **1.6**.

## 5. Checks on live data

| ID | Check | Computed | Result |
|---|---|---:|---|
| V1 | Bank's quote vs. money-market parity (±0.05%) | +5.64% | **FLAG**: the quote (§3) |
| V2 | Money market = CIP forward | 0 | PASS |
| V3 | Put kink at K_PUT ± one step | $0.00 | PASS |
| V4 | Put floor = K_PUT × FC_AMT + FV_PREM_PUT | $2,276,692.90 | PASS |
| V5 | Put moves continuously (≤ $23,079.00 per step + $0.01) | $23,079.00 | PASS |
| V6 | Forward and money market constant across the grid | 0 | PASS |
| V7 | Put–call parity vs. CIP (±0.5%) | +0.11% | PASS |
| V8 | Put–call parity vs. the bank's quote (±0.5%) | −5.24% | **FLAG**: the quote (§3) |
| V9 | No error cells, every tab | 0 | PASS |
| V10 | Every green and gray cell is a formula | 0 | PASS |
| V11 | Fixed terms unchanged | TRUE | PASS |
| V12 | Base case = Stage 4 hand-check (§5.1b) | $0.003 | PASS |
| V13 | Call kink at K_CALL ± one step | $0.00 | PASS |

V7 passing matters. The scenario's premiums were set for the October market, yet with strikes re-set to today's spot they still imply a forward within 0.11% of the CIP forward. Keeping them as an assumption does not distort the option results.

## 6. Sensitivity around the new spot

The grid recentred on 1.0990 without any edit to it.

| Shock | S_T | No hedge | Forward (quote) | Money market | Option (put) |
|---|---:|---:|---:|---:|---:|
| −5% | 1.044050 | 2,192,505.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| −4% | 1.055040 | 2,215,584.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| −3% | 1.066030 | 2,238,663.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| −2% | 1.077020 | 2,261,742.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| −1% | 1.088010 | 2,284,821.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| 0% | 1.099000 | 2,307,900.00 | 2,447,130.00 | 2,316,431.87 | 2,276,692.90 |
| +1% | 1.109990 | 2,330,979.00 | 2,447,130.00 | 2,316,431.87 | 2,299,771.90 |
| +2% | 1.120980 | 2,354,058.00 | 2,447,130.00 | 2,316,431.87 | 2,322,850.90 |
| +3% | 1.131970 | 2,377,137.00 | 2,447,130.00 | 2,316,431.87 | 2,345,929.90 |
| +4% | 1.142960 | 2,400,216.00 | 2,447,130.00 | 2,316,431.87 | 2,369,008.90 |
| +5% | 1.153950 | 2,423,295.00 | 2,447,130.00 | 2,316,431.87 | 2,392,087.90 |

- **The forward wins every row.** That is a symptom of the off-market quote, not a finding about hedging. At a CIP-consistent forward, it would match the money-market column.
- **Money market vs. no hedge.** The money-market hedge beats staying unhedged whenever the euro settles below 1.1031, which is `F_CIP`.
- **The put** guarantees $2,276,692.90 and keeps every dollar of upside above 1.0990, less its $31,207.10 carried premium.
- **The exposure is already costing money.** Unhedged, the receivable is worth $2,307,900 today, $38,220 less than at the Stage 2 spot. That loss has already happened.

## 7. Cross-check against the FX Hedging Lab

**Run:** 2026-11-20 at 22:44 HST, in the course's FX Hedging Lab ([adamwstauffer.github.io/ai-lms/fxlab.html](https://adamwstauffer.github.io/ai-lms/fxlab.html)). The Lab states its convention as simple interest, ACT/360, the same as the workbook.

**Inputs entered into the Lab:**
- Amount: EUR 2,100,000
- Days: 91
- Spot: 1.0990
- Forward: 1.1653
- USD rate: 4.32%
- EUR rate: 2.847%
- Put: strike 1.0990, premium 0.0147
- Call: strike 1.0990, premium 0.0199

The Lab rounds its displays to whole dollars, whole euros and four decimals.

| Output | Workbook (v1.6) | FX Lab | Difference | Resolution |
|---|---:|---:|---:|---|
| Forward-hedge proceeds | $2,447,130.00 | $2,447,130 | 0 | Match |
| Money market, step 1: EUR borrowed | €2,084,995.16 | €2,084,995 | rounding | Match |
| Money market, step 2: converted at spot | $2,291,409.68 | $2,291,410 | rounding | Match |
| Money market, step 3: proceeds | $2,316,431.87 | $2,316,432 | rounding | Match |
| Implied (CIP) forward | 1.103063 | 1.1031 | rounding | Match |
| Gap between quote and parity, USD | +$130,698.13 (`USD_FWD − USD_MM`) | −$130,698 | sign only | Match. The Lab reports implied minus quoted; the workbook reports quoted minus implied. |
| No-hedge column, all 11 grid rows | e.g. $2,192,505.00 at −5% | $2,192,505 at −5% | 0 | Match in every row |
| Put floor (S_T ≤ 1.0990) | $2,276,692.90 | $2,277,030 | **Lab +$337.10** | Convention: the workbook is right (below) |
| Put at S_T = 1.1540 (+5%) | $2,392,087.90 | $2,392,425 | **Lab +$337.10** | Same convention, the same $337.10 in every put row |
| Best strategy, all 11 rows | Forward | Forward | — | Match |
| Put premium cost today; call payoff | $30,870.00; call columns on `Options` | Not shown by the Lab | — | Not compared. The Lab describes the call only as the payable case. |

**The one difference: the put, $337.10 in every row.** The Lab nets the premium as paid at settlement, `MAX(S_T, K_PUT) × FC_AMT − PREM_PUT × FC_AMT`. The workbook carries the premium paid today forward to settlement at the USD rate, `FV_PREM_PUT = −PREM_PUT × FC_AMT × (1 + R_USD × 91/360)`. The gap is exactly that interest: $30,870.00 × 4.32% × 91/360 = **$337.10**.

I kept the workbook's version. The premium leaves the firm's account today, and spec §4 Step 4 carries it forward deliberately; the instructor's Stage 1 review called it "a careful touch". It is a stated convention, not an error in either tool, and at $337.10 it is 0.014% of the put's proceeds.

**The Lab's parity warning.** The Lab marks the $130,698 gap with "in a real model this means a formula error (wrong rate basis, inverted ratio, or mis-sized loan), not an arbitrage." I checked each of those against the comparison above:
- **Rate basis:** the Lab and workbook agree to the dollar on all three money-market steps and the implied forward, so the basis is not the cause.
- **Ratio:** the ratio is not inverted.
- **Loan:** the loan is sized correctly (€2,084,995.16 repays exactly €2,100,000.00).

That leaves the quoted forward itself. The gap is an input finding about the bank's quote (§3), and it is why I asked for a requote.

## 8. Assumptions, limitations and sources

- **Premiums:** scenario-given and kept as an assumption. **Strikes:** at the live spot.
- **Rates:** reference and benchmark rates, so best case for a corporate borrower and depositor. **Spot:** the ECB reference fix, not a dealer bid.
- **Not included:** bid–ask spreads, credit risk or late payment.
- **Horizon:** `T_DAYS` is 91 from the pricing date (settling 2027-02-19). The Stage 1 brief dated settlement as 2027-01-29, counting from 2026-10-30; the course keeps the 91-day horizon at each stage's pricing date.

**Sources** (all accessed 2026-11-20, about 22:30 HST):

- European Central Bank. *Euro foreign exchange reference rates: US dollar* (20 November 2026: 1.0990). https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/eurofxref-graph-usd.en.html
- U.S. Department of the Treasury. *Daily Treasury Par Yield Curve Rates*, November 2026 (11/20/2026, 3 Mo: 4.32). https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve&field_tdr_date_value_month=202611
- Board of Governors of the Federal Reserve System. *H.15 Selected Interest Rates*, release of 2026-11-19 (3-month CMT 4.34%; effective fed funds 3.88%). https://fred.stlouisfed.org/release/tables?eid=289&rid=18
- European Money Markets Institute, via euribor-rates.eu. *Current Euribor rates* (3 months, 19/11/2026: 2.847%). https://www.euribor-rates.eu/en/current-euribor-rates/ (matched by https://euriborrates.com/historical-euribor)
- FXEmpire. *EUR/USD Forward Rates*, as of 21:59 UTC, 20 November 2026 (cross-check only). https://www.fxempire.com/currencies/eur-usd/forward-rates
- Stauffer, A. (2026). *FX hedging project: Scenario 10, craft-whiskey* [Course material]. Shidler College of Business, University of Hawaiʻi at Mānoa.

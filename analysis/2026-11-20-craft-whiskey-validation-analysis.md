---
type: analysis
title: "Stage 5 Validation: An Independent LLM Run, Hand Verification and a Spec Retrospective"
scenario: craft-whiskey
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-20
status: committed          # committed | superseded
llm_used: "Claude Opus 5.5 (the independent run); Claude (drafting this note)"
---

# Stage 5 Validation: An Independent LLM Run, Hand Verification and a Spec Retrospective

**Created by:** Alexis Fuchs  
**Date Created:** 2026-11-20  
**Version:** 1.0  
**LLM Used:** Claude Opus 5.5 for the independent run (Part 1); Claude for drafting this note, reviewed by the author  
**Inputs tested:** [`capabilities/fx-hedging/spec.md`](../capabilities/fx-hedging/spec.md) v1.6 and [`data/2026-11-20-craft-whiskey-market-data-memo.md`](../data/2026-11-20-craft-whiskey-market-data-memo.md), both as committed at `1ae33d1` · **Compared against:** [`capabilities/fx-hedging/model.xlsx`](../capabilities/fx-hedging/model.xlsx) v1.6 · **Decision:** [`docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md`](../docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md)

---

## Summary

A fresh LLM, given only my spec and my market-data memo, reproduced every number the workbook produces, to the cent:
- all five strategies at three settlement rates;
- eleven intermediate values;
- every check it was able to run.

It also reached the same recommendation: sell the euros forward, at a confirmed requote. **There is no numeric discrepancy to diagnose.**

The run is still useful evidence, because of what the LLM had to *decide for itself*:
- which of two sets of input values to use;
- what day-count basis applies to a bond-equivalent rate;
- what the bank's quote really is;
- what decision threshold to use.

Each of those is a gap in my documents, and Part 4 turns them into spec v2 changes. Three outcomes recomputed by hand (Part 2b) reconcile to the workbook exactly.

---

## Part 1: The independent run

| Item | Detail |
|---|---|
| Tool and model | Claude Opus 5.5 ("extra" setting), in a fresh chat with no history, outside this project |
| Started | 2026-11-20, 23:07 HST |
| Documents provided | Exactly two, as file uploads: `spec.md` (v1.6) and `2026-11-20-craft-whiskey-market-data-memo.md` |
| Coaching | None: one prompt, no follow-up messages, no corrections, no workbook results pasted |
| Raw output | Appendix A, verbatim |

**Prompt (logged verbatim):**

> I'm attaching two documents: a model specification (spec.md) and a market-data memo. Using only these two documents, compute all the hedge outcomes for the receivable they describe: no hedge, the forward hedge, the money-market hedge (all three steps), the put and the call, across the sensitivity grid the specification defines. Run whichever of the specification's validation checks you can. Then recommend one hedging strategy to the CFO and explain your reasoning. Show your arithmetic. Where anything in the documents is ambiguous, state the assumption you made and continue rather than asking me.

---

## Part 2a: Comparison, LLM vs. workbook

### Strategy outcomes at three settlement rates

USD proceeds at settlement on EUR 2,100,000. The call is its net value on its own (`USD_CALL`).

| Strategy | S_T = 1.044050 (−5%) | S_T = 1.099000 (0%) | S_T = 1.153950 (+5%) | Workbook vs. LLM |
|---|---:|---:|---:|---|
| No hedge | 2,192,505.00 | 2,307,900.00 | 2,423,295.00 | Identical at all three |
| Forward (bank's quote 1.1653) | 2,447,130.00 | 2,447,130.00 | 2,447,130.00 | Identical |
| Money market | 2,316,431.87 | 2,316,431.87 | 2,316,431.87 | Identical |
| Put (K 1.0990) | 2,276,692.90 | 2,276,692.90 | 2,392,087.90 | Identical |
| Call (K 1.0990), net | −42,246.35 | −42,246.35 | +73,148.65 | Identical |

The full 11-row grids also agree in every cell, hedge-profit and winner columns included. The workbook's grid is on `Sensitivity`; the LLM's is in Appendix A, §5.

### Intermediate values

| Value | Workbook | LLM | Difference |
|---|---:|---:|---:|
| `DF_USD` | 1.010920 | 1.010920 | 0 |
| `DF_FC` | 1.007197 | 1.0071966 (shown to 7 d.p.) | rounding only |
| Step 1, `MM_FC_BORROW` | €2,084,995.16 | €2,084,995.16 | 0 |
| Step 2, `MM_USD_T0` | $2,291,409.68 | $2,291,409.68 | 0 |
| Step 3, `USD_MM` | $2,316,431.87 | $2,316,431.87 | 0 |
| `F_CIP` | 1.103063 | 1.103063 | 0 |
| `QUOTE_GAP` (rate / USD) | +0.0622 / $130,698.13 | 0.062237 / $130,698.13 | 0 |
| `F_PCP` | 1.104257 | 1.104257 | 0 |
| `FV_PREM_PUT` / `FV_PREM_CALL` | −$31,207.10 / −$42,246.35 | −$31,207.10 / −$42,246.35 | 0 |
| `PREM_PUT_USD` / `PREM_CALL_USD` | $30,870.00 / $41,790.00 | $30,870.00 / $41,790.00 | 0 |
| `tbl_Summary` (worst, best, at S0, cost, upside) | all four strategies | all four strategies | 0 |

### Everything that differed, diagnosed

| # | Difference | Diagnosis | What I did |
|---|---|---|---|
| 1 | **Checks.** The workbook shows 11 PASS / 2 FLAG. The LLM reported 9 PASS / 2 FLAG / 2 "not runnable" (V9, V10). | **Spec design.** V9 (no error cells) and V10 (every green or gray cell is a formula) test spreadsheet cells, so they cannot run from documents. The two FLAGs (V1, V8) are the same in both. | No error. Spec v2 labels such checks as workbook-only (Part 4, item 4). |
| 2 | **Inputs.** The LLM had to choose between the spec's §2.1 placeholder values and the memo's live values. It also noted that spec §1 still gives the October settlement date (2027-01-29) and value ($2,346,120). | **Spec ambiguity.** The LLM chose correctly, but it had to reason its way there. | Part 4, item 1. |
| 3 | **Rate basis.** The LLM applied ACT/360 to `R_USD` "as the spec says, even though it is a bond-equivalent yield". It sized the cost at $342.77, matching my memo. | **Spec ambiguity.** One `BASIS` serves two rates quoted on different bases. | Part 4, item 3. |
| 4 | **Requote rule.** For an unconfirmed quote, the LLM's rule is to book a requote in the market range 1.101–1.104 and get a competing quote if it comes in well below that. My memo's rule was to reconsider only below 1.0841 (the put floor as a rate). | **Judgment, not error.** Both are right for different questions: the LLM's rule tests whether the bank's price is fair; mine tests whether the put should replace the forward. | I added the LLM's competing-quote step to the decision memo. |
| 5 | **Extra analysis.** The LLM computed figures the workbook does not: break-evens (put vs. no hedge 1.084139, call 1.119117, put vs. a parity forward 1.117923), and the call held with the receivable (outcome range widening from $230,790 to $346,185). | **Scope, not error.** I checked each one by hand and all are correct. | Spec v2 adds the break-evens as outputs (Part 4, item 6). |

**Recommendations compared.** Both say: sell EUR 2,100,000 forward for value 2027-02-19, book at once if the bank confirms 1.1653 is executable, and otherwise use a fresh quote. Both rule out the put unless the CFO expects a euro rally above 1.1179. Both reject the money-market hedge on operating grounds, since it gives the same dollars as a forward at parity.

---

## Part 2b: Hand verification

Each outcome below is recomputed by hand from the named inputs, every step shown so it can be checked on a calculator with no Excel. The inputs: `FC_AMT` 2,100,000 · `T_DAYS` 91 · `BASIS` 360 · `S0_in` 1.0990 · `F0_in` 1.1653 · `R_USD` 0.0432 · `R_FC` 0.02847 · `K_PUT` = `K_CALL` 1.0990 · `PREM_PUT` 0.0147 · `PREM_CALL` 0.0199. The year fraction is `T_DAYS / BASIS` = 91 / 360 = 0.2527778.

| # | Outcome | Named-range formula | Arithmetic, step by step | By hand | Workbook | Diff. |
|---|---|---|---|---:|---:|---:|
| 1 | Forward proceeds | `USD_FWD = FC_AMT × F0_in` | 2,100,000 × 1.1653 | **$2,447,130.00** | $2,447,130.00 | 0 |
| 2a | Accumulation factors | `DF_USD = 1 + R_USD × T_DAYS/BASIS`; `DF_FC = 1 + R_FC × T_DAYS/BASIS` | 1 + 0.0432 × 0.2527778 = 1 + 0.0109200 = **1.0109200**; 1 + 0.02847 × 0.2527778 = 1 + 0.0071966 = **1.0071966** | — | 1.010920 / 1.007197 | 0 |
| 2b | Money market, step 1: borrow EUR | `MM_FC_BORROW = FC_AMT / DF_FC` | 2,100,000 ÷ 1.0071966 | **€2,084,995.16** | €2,084,995.16 | 0 |
| 2c | Step 2: convert at spot | `MM_USD_T0 = MM_FC_BORROW × S0_in` | 2,084,995.16 × 1.0990 | **$2,291,409.68** | $2,291,409.68 | 0 |
| 2d | Step 3: invest USD | `USD_MM = MM_USD_T0 × DF_USD` | 2,291,409.68 × 1.0109200 = 2,291,409.68 + 25,022.19 | **$2,316,431.87** | $2,316,431.87 | 0 |
| 2e | Loan check | `MM_FC_BORROW × DF_FC = FC_AMT` | 2,084,995.16 × 1.0071966 = 2,084,995.16 + 15,004.84 | **€2,100,000.00** | €2,100,000.00 | 0 |
| 3 | Put at S_T = 1.044050 (−5%) | `USD_PUT = MAX(K_PUT − S_T, 0) × FC_AMT + S_T × FC_AMT + FV_PREM_PUT`, with `FV_PREM_PUT = −PREM_PUT × FC_AMT × DF_USD` | payoff (1.0990 − 1.04405) × 2,100,000 = 0.05495 × 2,100,000 = 115,395.00; euros sold 1.04405 × 2,100,000 = 2,192,505.00; premium −(0.0147 × 2,100,000) × 1.01092 = −30,870.00 × 1.01092 = −31,207.10; total 115,395.00 + 2,192,505.00 − 31,207.10 | **$2,276,692.90** | $2,276,692.90 | 0 |
| 4 | Call at S_T = 1.153950 (+5%) | `USD_CALL = MAX(S_T − K_CALL, 0) × FC_AMT + FV_PREM_CALL` | (1.15395 − 1.0990) × 2,100,000 = 115,395.00; premium −(0.0199 × 2,100,000) × 1.01092 = −41,790.00 × 1.01092 = −42,246.35; total 115,395.00 − 42,246.35 | **+$73,148.65** | +$73,148.65 | 0 |
| 5 | Parity forward and the quote gap | `F_CIP = S0_in × DF_USD / DF_FC`; gap = `(F0_in − F_CIP) × FC_AMT` | 1.0990 × 1.01092 = 1.1110011; ÷ 1.0071966 = 1.103063; (1.1653 − 1.103063) × 2,100,000 = 0.062237 × 2,100,000 | **1.103063; $130,698.13** | 1.103063; $130,698.13 | 0 |

Outcome 5 ties two independent routes together. The same gap is `USD_FWD − USD_MM` = 2,447,130.00 − 2,316,431.87 = $130,698.13.

The put floor also needs no grid: `K_PUT × FC_AMT + FV_PREM_PUT` = 1.0990 × 2,100,000 − 31,207.10 = 2,307,900.00 − 31,207.10 = $2,276,692.90. That is Checks V4.

---

## Part 4: Spec retrospective

The calculation flow passed the production test. A model that had never seen my workbook rebuilt every formula from the named-range contract and §4, without a question.

The weaknesses are in what surrounds the formulas: dated values, labels, conventions and decision rules that my documents left to the reader. The LLM had to state an assumption at each one. Spec v2 would close all six:

1. **The spec mixes the model with its data.** §2.1 still prints the October placeholder values as the input table. §1 still says the receivable settles on 2027-01-29 and is worth $2,346,120. The LLM had to decide that the memo's live values override the spec's. It decided correctly, but a less careful reader would have used the October numbers.
   - **v2:** the spec defines structure only. §1 describes the exposure without dated values, and one line names the "inputs in force" document.
2. **The forward has two descriptions.** §2.1 still calls `F0_in` a "Scenario 10 indicative forward (set 2026-10-18 for teaching)". §5.2 and the memo call it the bank's quote. The LLM quoted the "set for teaching" wording as part of its case against the quote. That is a reasonable reading of my own words, and the wrong signal to send a CFO about a bank's price.
   - **v2:** one definition, "the bank's dated quote", plus an `F0_date` input, so a requote replaces it with a date and nothing else.
3. **One day-count basis for two differently quoted rates.** `BASIS` = 360 applies to both legs, but `R_USD` is a bond-equivalent yield. The LLM flagged it and sized it at $342.77. My own §6.2 improvement brief proposed splitting `BASIS` in October, and I never implemented it.
   - **v2:** `BASIS_USD` and `BASIS_FC`, with each rate's quoting basis written on its input row.
4. **Two checks cannot run outside the workbook.** V9 and V10 test cells, not finance, so an independent reader cannot run them, and the spec does not say so.
   - **v2:** each check is tagged *model logic* (reproducible from documents) or *workbook integrity* (spreadsheet only).
5. **My memo handed over the answers.** The market-data memo reports results in §3, §5, §6 and §7, so the LLM could check itself against them, and it said it did ("my figures match the memo's §5.1b and §6 to the cent"). Its arithmetic is its own: every step is shown, with its own rounding (`DF_FC` to seven decimals). Still, a match against a document that already contains the answers proves less than a blind match would.
   - **v2:** the market-data memo carries inputs and provenance only. Results live in the validation and decision documents, so the production test is blind.
6. **No decision thresholds.** The spec defines outcomes but no rule for choosing among them. The LLM invented a requote rule (1.101–1.104) and I invented another (1.0841). Neither is in the model.
   - **v2:** add named break-even outputs (`BE_PUT_VS_NOHEDGE` 1.0841, `BE_PUT_VS_LOCK` 1.1179, `BE_CALL` 1.1191) and a stated budget figure. The decision memo's thresholds then come from the workbook, not from whoever writes the memo.

---

## Appendix A: Raw LLM output (verbatim)

Copied from the independent run (Claude Opus 5.5, started 2026-11-20 23:07 HST). Unedited; tables are as copied.

```text
I used the Stage 4 live inputs throughout. My figures match the memo's §5.1b and §6 to the cent. I recommend a full forward hedge executed at a fresh, confirmed quote from the bank, not at the 1.1653 currently on file. The reasoning is at the end.

Assumptions where the documents leave room
Inputs: I used the Stage 4 live values (memo §1, spec §5.1b), not the spec §2.1 placeholders. The spec's §1 still gives the placeholder settlement date (2027-01-29) and value ($2,346,120). I counted 91 days from 2026-11-20, so settlement is 2027-02-19.
Spot: I used the ECB fix of 1.0990, which is the input in force, not the New York close of 1.09683.
USD rate: I applied 4.320% on ACT/360 as the spec says, even though it is a bond-equivalent yield. The cost of that choice is shown in §3.
Premiums: 0.0147 and 0.0199 are the October scenario values, kept as given. Each premium is paid today and carried forward to settlement at R_USD (spec §3), not netted at settlement as the course's FX Lab does.
Forward: F0_in stays as the bank's quote of 1.1653, per the course rule. For the recommendation I also tested a parity-consistent forward.
Call: I show the net value of the call on its own, as the spec's USD_CALL defines it. A call is not a hedge for a receivable.
Base case: I took it as FC_AMT × S0_in = $2,307,900, following spec §7's formula.
1. Derived inputs
T/BASIS       = 91 / 360                         = 0.252778
DF_USD        = 1 + 0.0432 × 91/360  = 1 + 3.9312/360  = 1.010920
DF_FC         = 1 + 0.02847 × 91/360 = 1 + 2.59077/360 = 1.0071966
PREM_PUT_USD  = 0.0147 × 2,100,000               = $30,870.00
PREM_CALL_USD = 0.0199 × 2,100,000               = $41,790.00
FV_PREM_PUT   = −30,870.00 × 1.010920 = −(30,870.00 + 337.10) = −$31,207.10
FV_PREM_CALL  = −41,790.00 × 1.010920 = −(41,790.00 + 456.35) = −$42,246.35
2. No hedge and forward
No hedge at S0   = 2,100,000 × 1.0990               = $2,307,900.00
One 1% grid step = 0.01 × 1.0990 × 2,100,000        = $23,079.00
USD_FWD          = 2,100,000 × 1.1653               = $2,447,130.00  (same in every row)
3. Money-market hedge
Step 1  Borrow EUR:   2,100,000 / 1.0071966              = €2,084,995.16
Step 2  Convert:      2,084,995.16 × 1.0990              = $2,291,409.68
Step 3  Invest:       2,291,409.68 × 1.010920
                      = 2,291,409.68 + 25,022.19         = $2,316,431.87
Loan check:           2,084,995.16 × 1.0071966
                      = 2,084,995.16 + 15,004.84         = €2,100,000.00 (the receivable repays it exactly)
F_CIP = 1.0990 × 1.010920 / 1.0071966 = 1.1110011 / 1.0071966 = 1.103063
QUOTE_GAP = 1.1653 − 1.103063 = 0.062237 (622.4 pips) × 2,100,000 = $130,698.13

Day-count cost. Converting the USD rate to a money-market basis gives 4.32% × 360/365 = 4.261%. Using it would cut the money-market proceeds by 0.000592 × 91/360 × $2,291,409.68 = $342.77.

4. Options
Put:   USD_PUT  = MAX(S_T, 1.0990) × 2,100,000 − 31,207.10
       Floor    = 2,307,900.00 − 31,207.10                  = $2,276,692.90
Call:  USD_CALL = MAX(0, S_T − 1.0990) × 2,100,000 − 42,246.35
F_PCP = 1.0990 + (0.0199 − 0.0147) × 1.010920 = 1.0990 + 0.005257 = 1.104257

Break-even settlement rates:

Comparison	Settlement rate	Change from today
Put beats no hedge below 1.0990 − 31,207.10/2.1M	1.084139	−1.35%
Money market beats no hedge below F_CIP	1.103063	+0.37%
Put beats money market (or a parity forward) above (2,316,431.87 + 31,207.10)/2.1M	1.117923	+1.72%
Call breaks even above 1.0990 + 42,246.35/2.1M	1.119117	+1.83%
5. Sensitivity grid

S_T = 1.0990 × (1 + k × 1%), for k = −5 … +5.

Proceeds at settlement (USD)

Shock	S_T	No hedge	Forward (quote)	Money market	Put	Call (net)
−5%	1.044050	2,192,505.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
−4%	1.055040	2,215,584.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
−3%	1.066030	2,238,663.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
−2%	1.077020	2,261,742.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
−1%	1.088010	2,284,821.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
0%	1.099000	2,307,900.00	2,447,130.00	2,316,431.87	2,276,692.90	−42,246.35
+1%	1.109990	2,330,979.00	2,447,130.00	2,316,431.87	2,299,771.90	−19,167.35
+2%	1.120980	2,354,058.00	2,447,130.00	2,316,431.87	2,322,850.90	3,911.65
+3%	1.131970	2,377,137.00	2,447,130.00	2,316,431.87	2,345,929.90	26,990.65
+4%	1.142960	2,400,216.00	2,447,130.00	2,316,431.87	2,369,008.90	50,069.65
+5%	1.153950	2,423,295.00	2,447,130.00	2,316,431.87	2,392,087.90	73,148.65

Each call row is (S_T − 1.0990) × 2.1M − 42,246.35. For example, at +5%: 115,395.00 − 42,246.35 = 73,148.65.

The call adds exposure instead of removing it. Held alongside the receivable, it widens the range of outcomes from $230,790 to $346,185 (from $2,150,258.65 at −5% to $2,496,443.65 at +5%).

Hedge profit vs. no hedge (USD) and winners

Shock	Forward	Money market	Put	Overall winner	Best active hedge
−5%	+254,625.00	+123,926.87	+84,187.90	Forward	Forward
−4%	+231,546.00	+100,847.87	+61,108.90	Forward	Forward
−3%	+208,467.00	+77,768.87	+38,029.90	Forward	Forward
−2%	+185,388.00	+54,689.87	+14,950.90	Forward	Forward
−1%	+162,309.00	+31,610.87	−8,128.10	Forward	Forward
0%	+139,230.00	+8,531.87	−31,207.10	Forward	Forward
+1%	+116,151.00	−14,547.13	−31,207.10	Forward	Forward
+2%	+93,072.00	−37,626.13	−31,207.10	Forward	Forward
+3%	+69,993.00	−60,705.13	−31,207.10	Forward	Forward
+4%	+46,914.00	−83,784.13	−31,207.10	Forward	Forward
+5%	+23,835.00	−106,863.13	−31,207.10	Forward	Forward

Summary (tbl_Summary)

Strategy	Worst case	Best case	At S0	Up-front cost	Upside kept
No hedge	2,192,505.00	2,423,295.00	2,307,900.00	None	115,395.00
Forward	2,447,130.00	2,447,130.00	2,447,130.00	None	0.00
Money market	2,316,431.87	2,316,431.87	2,316,431.87	None	0.00
Put	2,276,692.90	2,392,087.90	2,276,692.90	30,870.00	115,395.00

At a parity forward of 1.103063 the picture changes. The forward column would equal the money-market column. The locked hedge would win from −5% to 0%, and no hedge would win from +1% upward. Among the hedges, the put would beat the lock only from +2% up (above 1.1179).

6. Validation checks
ID	Check	My figure	Result
V1	Forward vs. money market within ±0.05%	2,447,130.00 / 2,316,431.87 − 1 = +5.642%	FLAG
V2	USD_MM / FC_AMT = F_CIP	2,316,431.87 / 2,100,000 = 1.103063. The two are algebraically identical.	PASS
V3	Put kink at 1.08801 / 1.0990 / 1.10999	2,276,692.90 / 2,276,692.90 / 2,299,771.90, all as expected	PASS
V4	Floor = K × FC + FV_PREM_PUT	2,307,900.00 − 31,207.10 = 2,276,692.90 = grid minimum. Valid because the lowest grid rate, 1.04405, is below the strike.	PASS
V5	Put step ≤ $23,079.01	Largest step $23,079.00; $0 below the strike	PASS
V6	Forward and money market constant	Max − min = 0 for both	PASS
V7	F_PCP vs. F_CIP within ±0.5%	1.104257 / 1.103063 − 1 = +0.108%	PASS
V8	F_PCP vs. the quote within ±0.5%	1.104257 / 1.1653 − 1 = −5.238%	FLAG
V9	No error cells	Not run: it tests the workbook's cells	—
V10	Green and gray cells are formulas	Not run: it tests the workbook's cells	—
V11	FC_AMT = 2,100,000 and T_DAYS = 91	Both documents agree	PASS
V12	Base case vs. §5.1b, within ±$0.01	All four strategies and every intermediate value match to the cent	PASS
V13	Call kink at 1.08801 / 1.0990 / 1.10999	−42,246.35 / −42,246.35 / −19,167.35	PASS

The result is 9 PASS, 2 FLAG, 2 not runnable. Both FLAGs point to the bank's quote, not to the arithmetic.

7. Recommendation

Sell the full EUR 2,100,000 forward for value 2027-02-19, at a fresh quote the bank confirms is executable.

Why not accept the grid at face value. The forward wins every row only because of the quote.

Three independent forwards agree within 30 pips of each other: CIP 1.103063, put–call parity 1.104257 and the live market 1.10127.
The quote is 610 to 640 pips above all three.
The spec itself calls the quote "indicative… set for teaching." It was already 442 pips off at Stage 2, and it didn't move when spot fell 1.63%.
If the quote were dealable, it would hand the firm $130,698 of riskless profit. No working market prices like that.

If the bank does confirm 1.1653 is executable for EUR 2.1 million, book it at once. It is the best outcome in every row.

Why a forward rather than the put.

At a parity forward the lock is about $2,316,432. That is $39,738.97 above the put's floor, and it needs no cash today, while the put costs $30,870.
The put beats the lock only if the euro ends above 1.1179, a rise of 1.72% in 91 days. It beats doing nothing only if the euro falls more than 1.35%.
The euro has already fallen 1.63% since 30 October, which has cost $38,220. Forward points also favor the firm: they run +40 to +45 pips because USD rates exceed EUR rates, so locking in pays rather than costs.
The put's premiums are October scenario figures, not live quotes.

The put is the right choice only if the CFO holds a firm view that the euro will rebound.

Why a forward rather than the money-market hedge.

At parity the two are identical, as V2 confirms.
But $2,316,431.87 is a best case. The firm borrows euros above Euribor and earns at or below the T-bill rate, and the day-count correction alone removes $342.77.
The money-market hedge also needs a €2.08 million loan and three transactions. A forward does the same job in one contract.

Keep the money-market hedge as the yardstick for judging the bank's requote.

Decision rule:

Bank confirms 1.1653 is executable: book it, locking $2,447,130.00.
Requote in the market range of 1.101 to 1.104: book it, locking about $2,312,100 to $2,318,400.
Requote well below 1.101: get a competing quote, or run the money-market hedge directly.
Don't leave the receivable open while waiting. Each further 1% fall costs $23,079.

A point to set the CFO's expectations. The October budget of $2,346,120 is out of reach at market rates. A parity forward locks about $29,688 below it. That loss came with the euro's fall; a hedge now protects today's value of roughly $2.31 million, not the October figure.
```

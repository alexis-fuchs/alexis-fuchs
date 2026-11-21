---
type: memo
title: "Decision: Sell the EUR 2.1M Receivable Forward"
scenario: craft-whiskey
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-20
status: committed          # committed | superseded
llm_used: "Claude"
---

# Decision: Sell the EUR 2.1M Receivable Forward

**To:** CFO  
**From:** Alexis Fuchs, Treasury Analyst  
**Created by:** Alexis Fuchs  
**Updated by:** Alexis Fuchs  
**Date Created:** 2026-11-20  
**Date Updated:** 2026-11-20  
**Version:** 1.0  
**LLM Used:** Claude (drafted from my validated workbook and market-data memo; reviewed by the author)

*Written after the analysis.* Brief: [2026-10-23](../briefs/2026-10-23-craft-whiskey-hedge-brief.md) · Market data: [2026-11-20](../../data/2026-11-20-craft-whiskey-market-data-memo.md) · Validation: [2026-11-20](../../analysis/2026-11-20-craft-whiskey-validation-analysis.md) · Workbook: [model.xlsx](../../capabilities/fx-hedging/model.xlsx) (v1.6)

---

## Executive Summary

**Sell the full EUR 2,100,000 forward to the payment date, 19 February 2027.** At the forward rate today's market supports (about 1.1031), that locks in **$2,316,432**. That is $8,532 more than the receivable is worth at today's spot, with no cash up front and no further exposure to a falling euro.

The bank's current quote (1.1653) would lock in $2,447,130, but it sits **622 pips (+5.64%, $130,698) above what today's interest rates imply**, and 640 pips above the live market forward. I have asked the bank to requote. The gap does not change the instrument: the forward beats the alternatives at either price. It changes only what we can budget. Book **$2,316,432** until the bank confirms its price in writing.

The put option costs $30,870 today and beats the forward only if the euro rises more than 1.7% by February.

---

## A. Exposure summary

The French hotel group owes us EUR 2,100,000. Priced on 20 November, the payment settles 91 days later, on 19 February 2027.

At today's ECB rate of 1.0990 dollars per euro, the receivable is worth **$2,307,900**. That is already **$38,220 less** than on 30 October, when the euro stood at 1.1172. Every further 1% fall in the euro costs about **$23,079**.

The October value of $2,346,120 is no longer reachable at market rates: a market-priced forward locks in $29,688 less. A hedge now protects today's value, not October's.

## B. Hedge outcomes

All figures are live data of 20 November 2026, from the validated workbook.

| Strategy | Dollars on 19 Feb 2027 | Cash today | What it means for the decision |
|---|---:|---:|---|
| **No hedge** | $2,192,505 (euro −5%) to $2,423,295 (euro +5%); $2,307,900 if unchanged | $0 | Free, but a ±5% move swings the result by **±$115,395**. We have already lost $38,220 waiting. |
| **Forward at the bank's quote (1.1653)** | $2,447,130, fixed | $0 | Best on paper in every scenario, which is the warning sign: no market price supports it (Section D). |
| **Forward at a market-consistent rate (≈1.1031)** | $2,316,432, fixed | $0 | Full certainty, no premium, and $8,532 above today's spot value. That gain is the interest gap between dollars (4.32%) and euros (2.847%). |
| **Money-market hedge** | $2,316,432, fixed | Borrow €2,084,995, convert, deposit $2,291,410 | Same dollars as a market-consistent forward, but it ties up a €2.1M credit line and assumes benchmark borrowing rates, so it is a best case. |
| **Put, strike 1.0990** | At least $2,276,693; more if the euro rises (less the $31,207 premium cost carried to settlement) | **−$30,870** | Insurance with upside, at a price: it ends up $39,739 below a market forward unless the euro rallies. |
| **Call, strike 1.0990** | Costs $41,790; pays only if the euro rises | **−$41,790** | Not a hedge for us. It profits in the same direction as the receivable and adds risk. It is the tool for a euro *payable*. |

The course's FX Hedging Lab and an independent AI run reproduced these numbers; details in the validation document.

## C. Sensitivity interpretation

**If the euro falls**, this is the risk we are hedging:
- The forward and the money-market hedge do not move.
- The put stops the loss at its floor of $2,276,693.
- Staying unhedged loses up to $115,395 at −5%.

In every falling scenario, a market-priced forward protects **$39,739 more than the put**. The put's premium and its strike just below the forward rate both work against it.

**If the euro stays flat**, the forward earns the interest gap (+$8,532 over spot), and the put is $31,207 worse off because of its premium.

**If the euro rises:**
- Staying unhedged beats a market forward once the euro passes 1.1031 (+0.37%).
- The put overtakes the forward only above **1.1179 (+1.72%)**.
- Below that level, the premium buys nothing.

**The trade-off in one line:** the forward is certainty at no premium; the put is flexibility for $30,870 (1.34% of the receivable); no hedge is free but carries ±$115k at ±5%. Our stated objective was to protect the budgeted dollars at the lowest cost with the least upside given up. The forward meets the first two parts outright. It gives up upside only above +0.37%, where the put would need +1.72% just to break even.

## D. Recommendation

**Sell EUR 2,100,000 forward for value 19 February 2027.**

**Do the bank's quote and the CIP forward agree? No.**

| Comparison | Rate gap | Gap on the EUR 2.1M receivable |
|---|---:|---:|
| Bank's quote 1.1653 vs. CIP forward 1.103063 | +0.0622 USD per EUR: **622 pips (+5.64%)** | **$130,698** |
| Bank's quote vs. live market 3-month forward 1.1013 | 640 pips (+5.81%) | $134,463 |

**What explains the gap.** The quote is stale or mispriced, not an opportunity:
- A dealer prices a forward as spot plus the interest-rate differential. Today that is about +41 to +44 pips, and our CIP forward and the live market forward agree within 18 pips.
- The bank's quote was set on 18 October. It sat 442 pips above parity even on 30 October, and it has not moved while the euro fell 1.6%.
- If 1.1653 were executable, we could lock in about $131,000 with no risk by selling euros forward and covering through the money market. A real dealer would not offer that.

**Does the gap change the recommendation? No.** The forward is the right instrument at either price. The gap changes how much we can book and the order of steps:

1. **Get the requote.** I requested it on 20 November, on pull request #2.
2. **If the bank confirms 1.1653 is executable** for EUR 2.1M to 19 February, execute at once and book $2,447,130.
3. **Otherwise, execute at the requote.** Near today's market (about 1.103), that books about $2,316,432. If the requote lands well below the market range of 1.101–1.104, get a competing quote before booking. The independent AI review raised this step, and I agree with it.
4. **Reconsider only if the requote comes in below 1.0841.** That is the put's floor expressed as a rate; below it, the put protects more than the forward locks in.

**Hypothesis check.** My brief expected the forward and the money-market hedge to agree within 0.5%. Against the bank's quote they do not: 5.64%. Against the live market forward they do: 0.16%, or $3,765. So the hypothesis holds for a market-priced forward, and the gap belongs to the quote. The requote will show whether that gap is real.

## E. Executive justification

- **Cash-flow stability:** one fixed dollar inflow on 19 February, whatever the euro does.
- **Budget certainty:** the dollar amount is fixed today. This removes a ±$115,395 swing at ±5% and stops the slide that has already cost $38,220 since 30 October.
- **Liquidity:** no cash leaves today. The put needs $30,870 up front; the money-market hedge needs a €2.08M loan and a matching dollar deposit. The forward uses only a forward-contract line with our bank.
- **Optionality:** we give up gains if the euro rises above 1.1031. Given the objective, and a euro already down 1.6% since 30 October, that is the right trade.
- **Premium cost:** none. The forward's only cost is that forgone upside. The put's $30,870 is spent whatever happens.
- **Customer risk:** the forward commits us to deliver euros on 19 February. If the hotel group pays late, we roll the forward with a short FX swap. It is small, but we should agree the terms with the bank now.
- **Accounting (optional):** the forward's mark-to-market and the receivable's revaluation both run through earnings and largely offset. Formal hedge accounting under ASC 815 is available if we document the hedge at inception.

## Triggers to revisit

- **The bank's requote arrives:** enter it as `F0_in`, re-run the workbook, and issue version 1.1 of this memo.
- **The requote is below 1.0841,** or the board expects a euro rally of more than 1.7% by February: the put becomes the better choice.
- **The hotel group signals a different payment date:** re-time the forward.

## Limitations & Next Steps

**Limitations:**
- Interest rates are benchmark rates, and spot is the ECB reference fix, not a dealer's bid.
- Option premiums are the scenario's given figures.
- No bid–ask spreads, credit risk or late payment are modelled.

**Next steps:**
1. Obtain the requote. *(me, on receipt)*
2. Confirm the forward line and swap terms with the bank. *(me)*
3. Approval to execute. *(CFO)*

## AI use

Claude built the workbook from my written specification. It also retrieved and cross-checked the market data, ran the FX Lab comparison, and drafted this memo. A fresh AI session given only my spec and market-data memo recomputed the analysis independently. I verified the key figures by hand. See [prompt-log.md](../../prompt-log.md) and the [validation analysis](../../analysis/2026-11-20-craft-whiskey-validation-analysis.md).

## References

Fuchs, A. (2026). *Market-data memo: Live inputs for the craft-whiskey hedge model* (2026-11-20). `data/2026-11-20-craft-whiskey-market-data-memo.md`.

Fuchs, A. (2026). *FX transaction hedge model, technical specification* (v1.6) and *model.xlsx* (v1.6). `capabilities/fx-hedging/`.

European Central Bank. (2026, November 20). *Euro foreign exchange reference rates: US dollar*. https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/eurofxref-graph-usd.en.html

U.S. Department of the Treasury. (2026, November 20). *Daily Treasury par yield curve rates*. https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve&field_tdr_date_value_month=202611

Stauffer, A. (2026). *FX hedging project: Scenario 10, craft-whiskey* [Course material]. Shidler College of Business, University of Hawaiʻi at Mānoa.

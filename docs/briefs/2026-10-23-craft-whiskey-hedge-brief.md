---
type: brief
title: "Protecting the Dollar Value of Our Euro Receivable"
scenario: craft-whiskey
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-30
status: committed          # committed | superseded
hypothesis: "I expect the forward and money-market hedge to lock in nearly the same dollars (within 0.5%) once live data replaces the placeholder forward, because both are priced off the same spot rate and the same U.S.–euro interest-rate gap."
llm_used: "Claude"
---

# Protecting the Dollar Value of Our Euro Receivable

**Created by:** Alexis Fuchs  
**Updated by:** Alexis Fuchs  
**Date Created:** 2026-10-30  
**Date Updated:** 2026-10-30  
**Version:** 1.0  
**LLM Used:** Claude (drafted from the course template; reviewed and edited by the author)

---

## Executive Summary (≤150 words)

A French luxury-hotel group owes us EUR 2,100,000 in 91 days. At today's exchange rate that is worth about $2.35 million, but the dollar amount is not fixed: every 1% the euro falls costs us roughly $23,500, and a 5% fall would leave us $117,000 short of budget. Three tools can protect this payment: a forward contract, a money-market hedge, and a put option. I am not recommending one yet. I propose a short, four-stage analysis that puts a dollar figure on each option across realistic exchange-rate moves and ends with a recommendation for your approval.

---

## Background & Objectives

We invoiced the hotel group EUR 2,100,000, payable in 91 days (about January 29, 2027). At today's rate of 1.1172 dollars per euro, that is worth $2,346,120.

Because we are receiving euros, a weaker euro hurts us. If the euro falls 5% before we are paid, we receive $117,306 less; a 10% fall costs $234,612. Moves of that size are common for the euro within a year.

**Objective:** protect the dollar value we have budgeted, at the lowest cost and with the least upside given up.

---

## Methods

| Tool | How it works | Pro | Con |
|---|---|---|---|
| **Forward** | Agree today to sell EUR 2.1M on the payment date at a fixed rate. At the quoted 1.1653, we receive $2,447,130. | Complete certainty; no up-front cost. | No gain if the euro rises; still bound if the customer pays late. The quote is indicative and needs confirming. |
| **Money-market hedge** | Borrow about EUR 2.09M today, convert to dollars, and invest them; the customer's payment repays the loan. Locks in about $2,354,262. | Same certainty as a forward, built from loans and deposits we control. | Uses EUR 2.09M of borrowing capacity; more transactions to set up. |
| **Put option** | Pay $30,870 today for the right, not the obligation, to sell EUR 2.1M at 1.1172. | A floor of about $2,314,938, with all the upside if the euro rises. | The premium is spent either way; if the euro holds steady, we are about $31,000 worse off than doing nothing. |

In short, the first two lock the price; the put is insurance we pay for and hope not to use.

---

## What I am assuming

- The hotel group pays in full on the due date.
- The quoted prices are available to us, and our bank will lend us euros.
- Transaction costs and taxes are left out.
- With more time, I would test the forward quote first: it sits about 4% above the rate today's interest rates imply.

---

## Hypothesis

I expect the forward and money-market hedge to lock in nearly the same dollars, within 0.5% (about $12,000), once live market data replaces the placeholder forward, because both are priced off the same spot rate and the same U.S.–euro interest-rate gap.

---

## How I would know I was wrong

If, with live Stage 4 data, the two still differ by more than 0.5%, the gap is real rather than a stale quote.

---

## Limitations & Next Steps

The analysis covers one payment date and the full amount, and excludes credit risk and transaction costs. I will carry out four stages:

1. **Model specification (Stage 2):** design the workbook — inputs, formulas, checks — before building it.
2. **AI-assisted build and audit (Stage 3):** generate the workbook from that design and verify its results.
3. **Live market data (Stage 4):** replace placeholder prices with sourced, dated data.
4. **Validation and decision memo (Stage 5):** test the results independently and bring you a recommendation to approve.

---

## References

Board of Governors of the Federal Reserve System. (2026, October 30). *Selected interest rates (daily), H.15*. https://www.federalreserve.gov/releases/h15/

European Central Bank. (2026, October 30). *Euro foreign exchange reference rates: US dollar*. https://data.ecb.europa.eu/key-figures/ecb-interest-rates-and-exchange-rates/exchange-rates

European Money Markets Institute. (2026, October 28). *3-month Euribor fixing*. Retrieved from https://euribor-rates.info/en/euribor-rate/3-month-rate/

Stauffer, A. (2026). *FX hedging project: Scenario 10, craft-whiskey* [Course material]. Shidler College of Business, University of Hawaiʻi at Mānoa.


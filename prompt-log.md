# Prompt log

A running record of AI sessions that changed a file in this repo.
One entry per session. Newest at the bottom. Past entries are never
edited or deleted; corrections go in a new entry.

---

## 2026-10-09 — Portfolio repo setup

**Tool:** Claude
**Files changed:** docs/briefs/README.md, docs/decisions/README.md,
capabilities/README.md, analysis/README.md, data/README.md,
models/README.md, CLAUDE.md, AGENTS.md, RESUME.md, prompt-log.md,
.gitignore

**What I asked:**
Help building the Stage 0, part 2 skeleton: the folder structure with a
stub README in each folder, starter files, a .gitignore, and a first
draft of AGENTS.md.

**What I kept:**
- Folder structure and stub READMEs, unchanged
- CLAUDE.md one-line pointer
- .gitignore for Office, OS, and secret files

**What I changed or rejected, and why:**
- AGENTS.md: rewrote the "About me", never-paste, and working
  preferences sections to fit my own background
  

**What I verified:**
- No folder named after a course, term, or week
- Every folder shows on github.com with a file inside
- README.md bio is my own writing, untouched by AI

---
## 2026-10-30 — FX hedging Stage 2: model specification

**Tool:** Claude
**Files changed:** capabilities/fx-hedging/spec.md, capabilities/fx-hedging/README.md,
capabilities/README.md, docs/briefs/README.md, docs/decisions/README.md,
analysis/README.md, data/README.md, models/README.md, CLAUDE.md, AGENTS.md,
README.md, prompt-log.md

**What I asked:**
Draft the Stage 2 model specification and capability README for Scenario 10
(craft-whiskey); find today's EURUSD spot and 3-month USD and EUR rates with
sources; recreate the Stage 0 folder stubs that were missing from the repo.

**What I kept:**
- spec.md: tab layout, named ranges, formulas, validation checks, hand-check values
- Capability README and folder stub READMEs
- Inputs: ECB spot 1.1172 (30 Oct), Fed H.15 3-month Treasury 4.00% (29 Oct),
  3-month Euribor 2.618% (28 Oct)

**What I changed or rejected, and why:**
- Fixed a paste error: spec.md first held the README text, and this entry
  first landed in agents.md

**What I verified:**
- Opened the ECB, Fed H.15, and Euribor links in spec.md and matched the three values
- All folders and files show on github.com

---
## 2026-11-13 — FX hedging Stage 3: AI-assisted build and audit

**Tool:** Claude (claude.ai, with a Python and LibreOffice workspace)
**Files changed:** capabilities/fx-hedging/model.xlsx, capabilities/fx-hedging/spec.md,
capabilities/fx-hedging/README.md, analysis/2026-11-13-craft-whiskey-build-audit-analysis.md,
README.md, prompt-log.md

**What I asked:**
Walk me through Stage 3. Claude built model.xlsx from the committed spec (commit d5f6046)
as written, with the course starter prompt as the instruction: every build-contract item
is binding (ten named ranges, formulas only, cover, legend, all three hedge families,
±5% sensitivity table with chart, validation checks in the workbook). It then audited
the build, fixed each defect in spec.md, regenerated the workbook, and drafted the audit note.

**What I kept:**
- model.xlsx, regenerated after each spec fix (spec v1.0 to v1.5): 379 formulas,
  37 named ranges, Checks at 10 PASS / 2 FLAG
- spec.md v1.1–v1.5: call payoff and premium costs (contract item 5), V9/V10 error
  checks, V3/V4 put checks, V5 tolerance, tbl_Summary column definitions
- Audit note with 5 defects found and fixed and 5 checks confirmed

**What I changed or rejected, and why:**
- Left V1 and V8 as FLAG instead of loosening them: they flag the placeholder forward
  and should clear at Stage 4
- Fixed every defect in spec.md and regenerated the workbook; nothing patched by hand,
  and nothing re-explained in chat instead of the spec

**What I verified:**
- Opened model.xlsx in Excel: Checks shows 10 PASS / 2 FLAG and no #NAME?
- Formulas → Name Manager lists the ten contract names, each on its Inputs row
- Changed S0_in and saw the Sensitivity grid and chart move; closed without saving
- All files show on github.com at the paths the Stage 3 brief gives

---
## 2026-11-13 — FX hedging Stage 3: upload-path correction

**Tool:** Claude Code
**Files changed:** capabilities/fx-hedging/spec.md, capabilities/fx-hedging/model.xlsx,
analysis/2026-11-13-craft-whiskey-build-audit-analysis.md, AGENTS.md, prompt-log.md;
removed the five misplaced step folders and the root copy of the audit note

**What I asked:**
Move Stage 3 fixes 1–5 and the audit note to the paths the Stage 3 brief gives; my web
uploads had put them in step folders and at the repo root, so capabilities/fx-hedging/
still held spec v1.0 and the first build.

**What I kept:**
- Fixes 1–5 moved into capabilities/fx-hedging/ in order, one commit each, contents
  unchanged
- Audit note moved into analysis/; its "Open model.xlsx" check ticked after the Excel
  checks below

**What I changed or rejected, and why:**
- Correction to the previous entry: that session also changed AGENTS.md (AI-drafting
  policy for the FX hedging project), which its Files changed line left out
- Correction to the previous entry: its line "All files show on github.com at the paths
  the Stage 3 brief gives" was wrong; the files were in step folders and at the repo root

**What I verified:**
- Each moved file's SHA-256 matches its step-folder original
- spec.md shows Version 1.5, and the workbook's Cover says built from spec.md v1.5
- Opened model.xlsx in Excel: Formulas → Name Manager shows FC_AMT, T_DAYS, S0_in, F0_in,
  R_USD, R_FC, K_PUT, K_CALL, PREM_PUT and PREM_CALL, each on Inputs!$C$5:$C$15
- Checks shows 10 PASS / 2 FLAG, "All results as expected?" = Yes, and no #NAME?
- Changed Inputs!C7 from 1.1172 to 1.09: the Sensitivity grid's first row showed S_T
  1.035500 and No hedge $2,174,550.00; closed without saving
- All files show on github.com at the paths the Stage 3 brief gives

---
## 2026-11-20 — FX hedging Stage 4: market data and population

**Tool:** Claude (claude.ai, with web search, a Python and LibreOffice workspace, and the
built-in browser for the FX Lab); a shell script Claude wrote, run in Terminal, for the commits
**Files changed:** data/2026-11-20-craft-whiskey-market-data-memo.md,
capabilities/fx-hedging/model.xlsx, capabilities/fx-hedging/spec.md,
capabilities/fx-hedging/README.md, analysis/2026-11-13-craft-whiskey-build-audit-analysis.md,
README.md, prompt-log.md; docs/reviews/ arrived by merging pull requests #1 and #2

**What I asked:**
Walk me through Stage 4. Claude searched for the 2026-11-20 EURUSD spot, the 3-month
USD and EUR rates and a live 3-month forward, and computed the CIP forward and the
quote gap independently. It then loaded the live values through the named input
cells, recorded what broke, fixed the structure in spec v1.6, ran the course's FX
Hedging Lab in the built-in browser with the same inputs, and drafted the market-data
memo and the requote request.

**What I kept:**
- Live inputs: ECB spot 1.0990 (20 Nov); Treasury 3-month CMT 4.32% (20 Nov close);
  3-month Euribor 2.847% (19 Nov fixing); strikes re-set to 1.0990
- F0_in kept at 1.1653 (the bank's quote) and both premiums kept as an assumption
- Four structural fixes and two additions (V13, Cover read-first line), all in spec v1.6
- FX Lab cross-check: forward and money market match to the dollar; the Lab's put is
  $337.10 higher because it does not carry the premium to settlement (kept the workbook's
  convention)
- Checks at 11 PASS / 2 FLAG; V1 and V8 reported in the memo, not forced to pass

**What I changed or rejected, and why:**
- Rejected replacing F0_in with the CIP forward: the course rule and the instructor's
  review say the quote stays; asked for a requote instead
- Rejected 1-year rates: the receivable is 91 days, and the brief asks for the tenor
- Kept the Treasury rate on its published bond-equivalent basis so it can be re-pulled,
  and sized the basis effect ($342.77) in the memo instead of converting it

**What I verified:**
- Opened model.xlsx in Excel: Checks shows 11 PASS / 2 FLAG and "All results as expected?" = Yes
- Posted the requote request on pull request #2 from my account, and merged review PRs #1 and #2

---
## 2026-11-20 — FX hedging Stage 5: validation, decision memo and repo polish

**Tool:** Claude Opus 5.5 ("extra" setting) for the independent run, in a fresh chat with
no history; Claude (claude.ai) for drafting; a shell script Claude wrote, run in Terminal,
for the commits
**Files changed:** analysis/2026-11-20-craft-whiskey-validation-analysis.md,
docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md, README.md,
capabilities/fx-hedging/README.md, docs/reviews/README.md, analysis/figures/README.md,
docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md, prompt-log.md

**What I asked:**
Independent run (started 23:07 HST), with only spec.md v1.6 and the market-data memo
uploaded, prompt verbatim:
"I'm attaching two documents: a model specification (spec.md) and a market-data memo.
Using only these two documents, compute all the hedge outcomes for the receivable they
describe: no hedge, the forward hedge, the money-market hedge (all three steps), the put
and the call, across the sensitivity grid the specification defines. Run whichever of the
specification's validation checks you can. Then recommend one hedging strategy to the CFO
and explain your reasoning. Show your arithmetic. Where anything in the documents is
ambiguous, state the assumption you made and continue rather than asking me."
Then, in this chat: walk me through Stage 5, compare the run with the workbook, verify the
key outcomes by hand, draft the decision memo and retrospective, and polish the repo.

**What I kept:**
- The independent run matched the workbook to the cent on every strategy, grid row and
  intermediate; its raw output is Appendix A of the validation note
- Recommendation: sell EUR 2,100,000 forward for 2027-02-19 at a confirmed requote
  (about $2,316,432 at market); the bank's 1.1653 is 622 pips ($130,698) above parity
- The LLM's competing-quote step and its point about the October budget, both added to
  the decision memo
- Six spec-v2 changes in the retrospective, each tied to an assumption the LLM had to make
- README project section linking every stage artifact; the Stage 1 review's optional
  edits to the brief (To line, rates in the money-market row)

**What I changed or rejected, and why:**
- Did not coach or re-run the LLM: the run is a production test of my documents
- Kept spec.md at v1.6: the retrospective describes v2 rather than changing the model
  the workbook was built from
- Left my bio untouched (AGENTS.md: the bio is mine to write)

**What I verified:**
- Gave the fresh chat exactly the two documents and sent no follow-up messages
- Re-checked the hand-verification table on a calculator
- Set the repo's one-line description

---


<!-- Template for new entries — copy below this line

## YYYY-MM-DD — Short title

**Tool:**
**Files changed:**

**What I asked:**

**What I kept:**

**What I changed or rejected, and why:**

**What I verified:**

---
-->

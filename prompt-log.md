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

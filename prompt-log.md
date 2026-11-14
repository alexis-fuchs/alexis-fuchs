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

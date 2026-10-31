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

---
-->

# AGENTS.md — AI conventions for this repo

## About this repository
Portfolio repository of Alexis Fuchs, marketing and international business senior at the
University of Hawaiʻi at Mānoa. It holds every engagement from my finance
and economics coursework, organized by capability, not by course.
This file is canonical; CLAUDE.md only points here.

## Where things are
- README.md — my bio and the index of engagements. Do not edit the bio.
- RESUME.md — my resume.
- prompt-log.md — record of AI sessions that changed a file.
- docs/briefs/ — before the work: scope and hypothesis.
- docs/decisions/ — after the work: the recommendation memo.
- capabilities/<slug>/ — one folder per capability; spec.md is written
  before the build, and the build lives next to it.
- analysis/ — findings and the charts they refer to.
- data/ — sourced inputs, each with its provenance recorded.
- models/ — Performance Ratios only.

## Naming
- Use the exact folder and filename given in the stage brief.
- Dated files use YYYY-MM-DD at the start of the name.
- Slugs are lowercase-with-hyphens, named for the capability
  (e.g. marginal-analysis), never for a course, term, or week.
- Never invent a path or a filename. If one isn't given, ask me.

## How I work
- I'm comfortable with code, Git, and AI tools; I'm newer to finance and
  economics. Explain finance concepts plainly; skip basic programming.
- Be concise and direct. Tell me plainly when something is wrong.
- Explain what you will change before changing it.
- If you're unsure, ask one question instead of guessing.
- Every statistic or figure you give me is a draft until I verify it
  against a source. Always say where a number came from.

## What you may and may not draft
May: explain concepts, critique my work, debug code and formulas,
quiz me, and draft mechanical files (folder stubs, templates,
boilerplate, chart code).
May not: draft my bio, briefs, analyses, memos, or reflections.
The first draft of those is always mine. You may review them.

## Documentation
When a change affects a document (README index, spec.md, data
provenance), update that document in the same commit as the change.

## Scope
Do only what I asked. If you notice something else that needs fixing,
tell me rather than fixing it.

## Commits
Every commit message says what changed and why, e.g.
"Add Q3 price data to data/ with source and retrieval date".

## Prompt log
Append one entry to prompt-log.md for each session that changed a file:
date, tool, what I asked, what I kept or rejected, and why.
Never edit or delete past entries.

## Never include
Never put any of the following in this repo or in a prompt:
- Personal data about other people: classmates, coworkers, customers.
- My own identifiers: SSN, student ID, license, address, phone, DOB.
- Credentials: passwords, API keys, tokens, .env contents.
- Licensed or copyrighted material: textbook chapters, publisher
  slides, paid datasets. Cite them instead.
- Anything covered by a confidentiality agreement.
If any of this appears in a file or in our conversation, stop and tell
me rather than committing it.

## Mistakes to avoid
Append-only. Add a line each time something goes wrong; never delete.
- (none yet)

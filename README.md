## Hi there 👋
I am a fourth-year student at the University of Hawaiʻi at Mānoa’s Shidler College of Business, double majoring in Marketing and International Business and expected to graduate in December 2026. Throughout my time at university, I have developed a strong foundation in marketing principles, business strategy, market research, consumer behavior, and international business.

Through coursework, group projects, and professional experience, I have gained experience analyzing business challenges, developing marketing strategies, conducting research, and presenting recommendations. I enjoy applying concepts learned in the classroom to real-world business situations and collaborating with others to develop practical solutions. My academic experience has also strengthened my communication, critical thinking, and problem-solving skills.

In addition to my studies, I have gained hands-on experience through marketing projects and an internship with Japan Airlines, where I contributed to research, planning, and presentations related to marketing the Honolulu Marathon. These experiences have allowed me to apply my academic knowledge in a professional setting and gain a better understanding of marketing and business operations.

I am interested in continuing to develop my skills and gaining experience in marketing, business development, and brand strategy. I look forward to applying my education and experience to future opportunities while continuing to learn and grow professionally.

- Résumé: [RESUME.md](RESUME.md)
- How I work with AI: [AGENTS.md](AGENTS.md) · [prompt-log.md](prompt-log.md)

## Engagements

Each engagement is one question answered end to end: a **brief** written before the work, a **decision** written after it, and the capability, data and analysis that connect the two.

| # | Question | Brief (before) | Decision (after) | Capability |
|---|----------|----------------|------------------|------------|
| 1 | How should a U.S. craft-whiskey distiller protect a EUR 2,100,000 receivable due in 91 days? (FX hedging, Scenario 10) | [brief](docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md) | [decision: sell the euros forward](docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md) | [fx-hedging](capabilities/fx-hedging/) |

### FX hedging, stage by stage

| Stage | Deliverable | Files |
|-------|-------------|-------|
| 1 | Executive brief: the exposure and the plan | [brief](docs/briefs/2026-10-23-craft-whiskey-hedge-brief.md) |
| 2 | Model specification: named ranges, formulas, validation rules | [spec.md](capabilities/fx-hedging/spec.md) (v1.6) |
| 3 | AI-assisted build and audit | [model.xlsx](capabilities/fx-hedging/model.xlsx) · [build audit](analysis/2026-11-13-craft-whiskey-build-audit-analysis.md) |
| 4 | Live market data and population | [market-data memo](data/2026-11-20-craft-whiskey-market-data-memo.md) |
| 5 | Independent validation and the CFO decision memo | [validation](analysis/2026-11-20-craft-whiskey-validation-analysis.md) · [decision memo](docs/decisions/2026-11-20-craft-whiskey-hedge-decision-memo.md) |
| — | Instructor reviews, merged from pull requests | [docs/reviews/](docs/reviews/) |

## Capabilities

| Capability | What it does | Files |
|------------|--------------|-------|
| [fx-hedging](capabilities/fx-hedging/) | Compares the USD outcome of hedging a euro receivable with a forward, a money-market hedge and options against staying unhedged, with sourced live inputs and built-in validation checks | [README](capabilities/fx-hedging/README.md) · [spec](capabilities/fx-hedging/spec.md) · [model.xlsx](capabilities/fx-hedging/model.xlsx) |

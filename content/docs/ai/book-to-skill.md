---
weight: 5087
title: "book-to-skill"
description: "Turning a technical book, a docs folder, or a stack of papers into a skill your agent loads a chapter of when it needs one."
icon: "menu_book"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

The [design skills](/docs/ai/frontend-design-skills/) on the previous page are other
people's expertise, packaged. [book-to-skill](https://github.com/virgiliojr94/book-to-skill)
packages **yours** — it points at a book, a folder, or a glob and produces a structured
skill your agent can query.

The alternative you've probably tried is dumping a PDF into context, where the agent
re-reads the table of contents on every turn and you pay for a book to answer a paragraph.

## Install and run

```bash
npx skills add virgiliojr94/book-to-skill
```

```
/book-to-skill ./designing-data-intensive-applications.pdf
```

Then, later, in any project:

```
/designing-data-intensive-applications replication
```

It reads the chapter that actually covers replication and answers from the text. MIT
licensed, and it works with any host that reads the open Agent Skills `SKILL.md` format —
Claude Code, Copilot CLI, Amp, Hermes.

## What comes out

| File | What's in it | Size |
|---|---|---|
| `SKILL.md` | Core mental models and a chapter index | ~4,000 tokens |
| `chapters/ch01-*.md` … | One file per chapter | ~1,000 tokens each |
| `glossary.md` | Key terms, alphabetical, with chapter references | ~1,500 tokens |
| `patterns.md` | Techniques, algorithms, design patterns | ~2,000 tokens |
| `cheatsheet.md` | Decision tables and quick-reference rules | ~1,000 tokens |

The design decision that matters: **chapter files load on demand.** Only the index and the
mental models sit in context; the chapter arrives when a question needs it. The project
calls the alternative the "discovery loop tax" and measures its own conversions at 24–51×
fewer tokens than dumping the book in — their benchmark, on their books, but the shape of
the claim is straightforwardly right.

Extraction is a deterministic Python step; the structuring is your agent following a spec.

## It isn't really about books

The input is any prose you re-read often enough to wish you'd memorised:

| Source | Becomes |
|---|---|
| A SQL or React book | A reference skill for that stack |
| Your `docs/` folder, ADRs, runbooks | A skill that answers "how do we do this here?" |
| A brand and tone-of-voice guide | A skill the whole team queries instead of skimming 60 pages |
| RFCs, API contracts, compliance docs | Standards you consult but never memorise |
| A stack of papers plus your notes | One merged skill, folded into as new material lands |
| Certification material | A study skill you can drill against |

Internal documentation is the strongest case, because it's the knowledge no model was
trained on and the thing new people ask about most.

## Two things that will come up

- **Scanned PDFs need OCR first.** A book that's page images has no text layer to
  extract. The tool checks the first pages and stops with an explanation rather than
  producing an empty skill — run `ocrmypdf input.pdf output.pdf` and convert the result.
- **A distilled skill is notes, not the book.** It carries frameworks, definitions, and
  decision rules, deliberately not raw passages. That's the right call for context size,
  and it means the skill won't answer a question the summary dropped. Keep the book.

## The copyright part, briefly

The project ships no book content — it's a converter you point at files you already have,
and processing is local. The output is a synthesized derivative: treat a generated skill
the way you'd treat handwritten study notes.

**Which means: don't redistribute skills built from copyrighted books.** Keep those
private. Skills from your company's internal docs, your own writing, or openly licensed
material are yours to share within whatever licence they carry. If your agent's model runs
in the cloud, the text you feed it follows that provider's terms like any other prompt.

## Next

The most unexpected thing on this list — an agent that edits video →
[OpenMontage](/docs/ai/openmontage/)

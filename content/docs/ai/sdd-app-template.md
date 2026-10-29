---
weight: 51960
title: "sdd-app-template"
description: "Two files, not twelve skills — a CLAUDE.md and a four-prompt script that force a spec and a failing test to exist before an agent writes a line of your app."
icon: "fact_check"
date: "2026-10-29"
lastmod: "2026-10-29"
draft: false
---

[Agentic SDLC Kit](/docs/ai/agentic-sdlc-kit/) is twelve skills and a shared ID
vocabulary for a project you intend to keep for years.
[sdd-app-template](https://github.com/jeonck/sdd-app-template) is the other end of that
spectrum: **two files** — `CLAUDE.md` and `sdd-basic.md` — that you drop into a new
repo before Claude Code writes anything, to get the one rule that matters without the
rest of the machinery:

> No source code changes until a spec exists and has been reviewed in `/specs/`.

Typos, comments, and one-line bug fixes are explicitly exempted. Everything else — any
new feature, any behaviour change — waits for a spec first.

## What's actually in it

`CLAUDE.md` is the governing contract, built on two named influences:

- **Process — [Superpowers](https://github.com/obra/superpowers).** The repo doesn't
  bundle this skill framework; it assumes you've installed it separately, and its
  `CLAUDE.md` just invokes the Brainstorming → Plan → TDD → Review flow Superpowers
  already provides. Without Superpowers installed, the referenced skill names won't
  resolve to anything — read this template as a thin contract layered on top of that
  framework, not a replacement for it.
- **Philosophy — "Ponytail" (minimalism).** This is the template's own name for its
  counterweight principle, not an established outside methodology — don't go looking
  for a wider "Ponytail" movement. The rule it encodes: avoid over-engineering,
  unnecessary abstraction, and future-proofing for needs you don't have yet. Fifty
  clear lines beat two hundred lines of framework ceremony.

`sdd-basic.md` is the four-prompt script that actually drives the two together,
written in Korean, meant to be copy-pasted into Claude Code one stage at a time:

| Stage | What you paste in | What comes out |
|---|---|---|
| 1. Brainstorm & spec | "I want to build: *[feature]*. Ask me 3–5 questions about edge cases, data model, API shape, acceptance criteria before writing any code." | `/specs/feature-spec.md` |
| 2. Minimal design review | Apply Ponytail to the spec and codebase: what's reusable, what's unnecessary, simplify the plan | An updated **Implementation Plan** section in the same spec |
| 3. TDD implementation | RED: write the failing test for each acceptance criterion first. GREEN: the smallest code that passes it. Nothing speculative. | Passing tests, minimal code |
| 4. Verify & review | Checklist every acceptance criterion against the spec; strip debug comments, unused variables, duplicate logic introduced *by this change only* | A completion report |

Stage 4's cleanup is explicitly scoped to code this change touched — it's a rule against
drive-by refactoring of everything else in the repo, not a general tidy-up pass.

## Using it

```bash
# Copy the two files into a fresh project
curl -O https://raw.githubusercontent.com/jeonck/sdd-app-template/main/CLAUDE.md
curl -O https://raw.githubusercontent.com/jeonck/sdd-app-template/main/sdd-basic.md
mkdir specs
```

Then fill in the four placeholders at the bottom of `CLAUDE.md` — Tech Stack, Build
Command, Test Command, Lint Command — all shipped as literal `TBD`, since the template
is intentionally stack-agnostic. Open Claude Code in the project root and work through
`sdd-basic.md`'s four prompts in order for each new feature.

## What this deliberately isn't

No ID system, no traceability matrix, no automated verification stage, no `/skill`
install — this is prose, not tooling. Compare it against [Agentic SDLC Kit
](/docs/ai/agentic-sdlc-kit/)'s acceptance-verification skill, which re-derives every
check from source and re-runs every suite cold: nothing here enforces that an agent
*actually* followed the four stages in order, or that a "spec review" happened beyond
you reading the file. The whole mechanism is a convention you and the agent agree to —
closer to a project's style guide than to a pipeline with a gate.

That's also the honest selling point. If Agentic SDLC Kit is more process than your
side project needs, this is the version that costs you two files and five minutes to
adopt, and does the one thing most worth doing before an agent starts typing: **make it
write down what it's building, before it builds it.**

## Before you adopt it as-is

- **The repo itself is minimal.** Its own README is a single line; everything that
  matters is in `CLAUDE.md` and `sdd-basic.md`, which is also the whole review surface —
  read both files directly rather than this summary before committing to the workflow.
- **The prompts are Korean.** Translate them into your own working language, or have
  Claude Code do it on first use, rather than pasting them unmodified into an English
  session.
- **No LICENSE file is in the repo** at the time of writing — check before reusing the
  text verbatim in something you redistribute.

## Next

The same CLI, pointed at finding you a job instead of building software →
[career-ops](/docs/ai/career-ops/)

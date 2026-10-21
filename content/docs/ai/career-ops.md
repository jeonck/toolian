---
weight: 51975
title: "career-ops"
description: "An AI job-search agent that runs as a skill in your coding CLI — it scores postings against your real CV, drafts the resume and cover letter, and never clicks Submit."
icon: "work"
date: "2026-10-21"
lastmod: "2026-10-21"
draft: false
---

[Agentic SDLC Kit](/docs/ai/agentic-sdlc-kit/) turns an agent into a disciplined process
for building software. [career-ops](https://github.com/career-ops-hq/career-ops) turns
the same CLI you already have open into a disciplined process for **finding a job** —
as a set of skills, not a separate app. MIT licensed, free for candidates.

The origin story is in the README rather than the marketing page: the author evaluated
740 listings, applied to 68, got 12 interviews and one offer — then open-sourced the tool
that did the filtering. The design decision that follows from that is the whole point:
**it drafts, it never submits.** `prepare-application.mjs` fills a form; there is no code
path anywhere in the repo that calls Submit for you.

## Install

```bash
npx @santifer/career-ops init
cd career-ops
claude   # or codex / qwen / opencode / grok — open the AI CLI you already use
```

First launch interviews you in chat for your CV, profile and target roles — nothing to
hand-edit before you start. Paste a job URL or description and it auto-detects what to
do with it. A `git clone` path with manual config exists if you'd rather not run
`npx`, and `npm i -g @santifer/career-ops` gives a persistent `career-ops` command once
you've got a project folder going.

## What it actually does with a posting

1. **Checks it's still real** — still open, is it a repost, any ghost-job signal — before
   you spend an evening on it.
2. **Scores the fit against your actual CV**, with each requirement's weight labelled by
   where it came from: the posting's own wording, its structure, or an estimate — and an
   estimate can never land in the top band. Score below 4.0/5 and it tells you to skip;
   you can override, and it says so when you do.
3. **Drafts the CV and cover letter** as an ATS-readable PDF from your real experience,
   plus the answers to screening questions.
4. **Finds who to contact** and drafts a sub-300-character outreach note — but never
   sends it.
5. **Tracks the whole funnel** and, after a run of rejections, names the pattern: which
   ATS channel is converting, which skill gap keeps coming up.

Paste one job tonight and see which of those five you actually need; the full pipeline
runs end to end from one URL, or you can invoke `/career-ops pdf`, `/career-ops cover`,
`/career-ops interview-prep` and the rest individually.

## The part worth trusting it with your CV over

**It must reformulate, never fabricate.** The PDF generator blocks output whose numbers
or claims don't trace back to your own CV or story bank, unless you explicitly pass
`--skip-fact-check`. The project is candid about where that check doesn't reach yet —
it doesn't verify job titles or judge a rewording as faithful versus invented, and both
gaps are open, tracked issues rather than silently-accepted risk. Read every CV before
you send it regardless; the tool's own FAQ says the same thing.

**Nothing phones home.** Your CV goes from your machine straight to whichever AI
provider you configured, and nowhere else — no telemetry, no backend of the project's
own. The only public ledger is the repo itself.

## Runs on whatever CLI you already pay for

Claude Code, Codex, OpenCode, Antigravity, Grok Build CLI, Qwen, Pi, GitHub Copilot — any
CLI implementing the open Agent Skill Standard, through a shared command router. No
Anthropic key, no OpenAI key required: a standalone Gemini script covers evaluation with
nothing but a free [AI Studio](https://aistudio.google.com/apikey) key, and the docs
cover running it on local models through Ollama at zero marginal cost per application.

One gotcha worth knowing before it burns API credits you didn't mean to spend: if
`ANTHROPIC_API_KEY` is set in your shell, Claude Code bills to that key **instead of**
your logged-in Pro/Max subscription. Unset it and `/login` again if you're on a plan.

## What it won't do, on purpose

- No auto-submit, ever — the human-in-the-loop line is marked in the README as
  non-negotiable, not a setting to flip.
- No email sending — drafts only, no mail transport in the codebase.
- No pushing you to apply under its own 4.0/5 bar — it will say so if you override it.

A filter, not a spray-and-pray bot. If what you actually want is the other kind, this
isn't it — and that's deliberate.

## Next

Knowledge instead of roles — five skills that give an agent taste →
[Frontend Design Skills](/docs/ai/frontend-design-skills/)

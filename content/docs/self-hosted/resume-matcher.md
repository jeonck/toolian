---
weight: 160600
title: "Resume Matcher"
description: "A self-hosted resume tailor with a plain-code ATS score (no LLM call), bring-your-own model, and a Docker image that keeps your resume in your own database."
icon: "description"
date: "2026-10-21"
lastmod: "2026-10-21"
draft: false
---

[career-ops](/docs/ai/career-ops/) lives inside the AI coding CLI you already have open.
[Resume Matcher](https://github.com/srbhr/Resume-Matcher) is the other shape of the same
job: a self-hosted web app — FastAPI backend, Next.js frontend, SQLite — that you run on
your own machine with Docker or from source. Apache-2.0.

Build a master resume once, paste a job description, get a tailored resume and cover
letter back, with no account and no cloud copy of your CV sitting on someone else's
server.

## Run it

```bash
docker run --name resume-matcher \
  -p 3000:3000 \
  -v resume-data:/app/backend/data \
  ghcr.io/srbhr/resume-matcher:latest
```

Open `http://localhost:3000` and pick an AI provider in Settings. Pin a version in
production (`:1.3.0` rather than `:latest`); Docker only publishes to `127.0.0.1` by
default, so reaching it from another device needs the extra step in
[SETUP.md](https://github.com/srbhr/Resume-Matcher/blob/main/SETUP.md#opening-it-from-another-device).

From source instead: Python 3.13+, Node 24+, `uv`, then a backend and a frontend process:

```bash
git clone https://github.com/srbhr/Resume-Matcher.git && cd Resume-Matcher
cd apps/backend && cp .env.example .env && uv sync && uv run app     # terminal 1
cd apps/frontend && npm install && npm run dev                       # terminal 2
```

## No model lock-in, including "none"

Bring an API key for OpenAI, Anthropic, Gemini, DeepSeek, OpenRouter or Groq, or point it
at Ollama so your resume's content never leaves the box. The option worth knowing about:
**your coding agent can do the writing instead.** Open Claude Code, Codex, Cursor or
Copilot in the project folder and ask it to tailor your resume — the agent does the model
work in its own app, and Resume Matcher only stores, checks and renders the result. No
API key, nothing routed through this app's own backend at all.

(There's also a "Claude subscription via Claude Code" provider and a ChatGPT-plan sign-in
for routing through a chat subscription instead of a metered key — both come with the
usage-limits and provider-ToS caveats you'd expect, spelled out in
[SETUP.md](https://github.com/srbhr/Resume-Matcher/blob/main/SETUP.md).)

## The score that isn't an LLM guessing

The feature worth the whole page: **the ATS score and the ATS check are plain code, zero
model calls.** The score (0–100) is a weighted formula — keyword match 40%, skills
coverage 20%, job-title match 15%, section completeness 15%, date consistency 10% — and
the check renders your exported PDF and reads it back the way an actual applicant
tracking system's text extractor would, reporting parseability, reading order, and
whether your contact details and section headings survive the round trip. Deterministic,
reproducible, and exactly as fast as pdfminer — no provider, no rate limit, no "the model
felt generous today."

The tailoring prompts carry the same non-fabrication discipline as career-ops: additions
are checked against your master resume, with the missing keywords listed separately from
the ones you already have, so you extend the resume with what's true rather than what
scores well.

## Everything else in the box

A **Tailoring Queue** processes several job descriptions in the background against your
master resume, detecting role, company and job ID without an AI call, and resumes after
a restart since queued jobs live in the database rather than memory. Ten resume templates
(Deedy, LaTeX-style, single/two-column) export as text-based A4/Letter PDFs an ATS can
actually parse, seven of them also as Overleaf-ready LaTeX. Cover letters and
resume-grounded interview prep generate from the same tailored draft. The UI and
generated content both support English, Spanish, Chinese, Japanese, Brazilian
Portuguese, French and Korean.

An [MCP server](https://github.com/srbhr/Resume-Matcher/blob/main/SETUP.md#use-resume-matcher-from-claude-code-or-claude-desktop-mcp)
lets Claude Code or Claude Desktop write the tailored resume directly while this app
handles storage, the fact-check, and rendering — the same "bring your own agent" idea as
the coding-agent provider above, reached a different way.

## Next

One app doing one job well; the opposite shape is a whole learning platform →
[DeepTutor](/docs/self-hosted/deeptutor/)

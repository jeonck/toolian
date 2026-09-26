---
weight: 10090
title: "Book Writer"
description: "A Claude Code harness of 11 agents that takes a topic and returns a finished EPUB — research, plan, chapters, fact-check, acceptance gate, cover, build."
icon: "auto_stories"
date: "2026-09-26"
lastmod: "2026-09-26"
draft: false
---

Everything else in this category helps you write. [Book Writer](https://github.com/tobyilee/book-writer)
writes — you hand it a topic, an audience, and a rough length, and it comes back with a
`.epub` and a ready-to-post book description. MIT licensed, runs on
[Claude Code](/docs/ai/claude-code/) and nothing else: clone the repo, open Claude Code
in it, and `.claude/agents/`, `.claude/skills/`, and `CLAUDE.md` are picked up
automatically.

## The run

```bash
git clone https://github.com/tobyilee/book-writer.git
cd book-writer
brew install pandoc epubcheck    # pandoc ≥ 3.0 required; epubcheck optional but do it
claude
```

Then just describe the book:

```
주제: 효과적인 SQL 쿼리 튜닝
주요 내용: 실행 계획 읽기, 인덱스 설계, N+1 회피, 실전 사례
대상 독자: 백엔드 주니어 개발자 (SQL 기본은 아는 수준)
분량: 150페이지 정도
이 주제로 책 써줘.
```

Six phases follow, with you in the loop at exactly one mandatory point:

| Phase | What happens |
|---|---|
| 1 · Research | Web, paper, and community researchers fan out in parallel; a lead synthesises `01_reference.md` |
| 2 · Plan | Chapter arc worked backwards from the reader's journey, with the rejected alternatives written down |
| 3 · Approve | **You approve the plan.** Optionally a reviewer critiques it on five axes first |
| 4 · Write | Up to four chapter writers on consecutive batches; each batch is fact-checked or continuity-checked the moment it lands |
| 4.5 · Acceptance | A reviewer in a **fresh context** judges the whole book. BLOCK means no build |
| 5 · Ship | Cover generated in the background, pandoc assembles EPUB 3, epubcheck validates |

Output is a pair at the project root: `{title}-v1.0.0.epub` and a matching `.md` blurb
with logline, audience, table of contents, and author bio — written to be pasted
straight into a store page or a blog.

## Why it's worth a look even if you never write a book

Three design choices here are worth stealing for any long-running agent pipeline:

**The acceptance gate runs in a fresh context.** The agent that assembled the manuscript
does not get to approve it. A separate reviewer checks chapter completeness, forbidden
markers (`(사실 확인 필요)`, `[리서치 공백]`, `[미완성]`), claims against the research
ledger, and cross-references — and a BLOCK verdict stops the build. Self-approval is the
most common failure in agent chains, and this is the cheap structural fix.

**Verification is scoped to what the writer can't see.** Rather than stacking review
round-trips, only two things get independent checking: factual errors in what the writer
just wrote, and conflicts with other chapters. The reasoning is stated plainly in the
README — a capable model given clear criteria self-checks as it writes, so spend the
review budget where self-checking structurally can't reach.

**Research files are kept as a ledger.** `research/*.md` survives re-runs, because the
fact-checker diffs claims against it. Provenance you can point at, not a citation the
model recalled.

## Genre profiles

Voice, structure, and review criteria live in `profiles/{genre}/` as three files —
`voice.md`, `scaffolds.md`, `style-checklist.md`, the last shared between the writers and
the acceptance gate.

| genre | For | Reviewed by |
|---|---|---|
| `tech-book` (default) | Technical books | fact-checker, on claims generally |
| `practical` | Cooking, travel, DIY | fact-checker, on safety/health/legal facts |
| `narrative` | Fiction | continuity-keeper against a `story_bible.md` |
| `essay` | Essays | style checklist only |

Genre is auto-detected in Phase 0 and confirmed, or pinned with `장르: {value}` in the
prompt. Changing tone means editing one `voice.md`; a new genre is three files plus a row
in `profiles/_registry.md`.

## The learning loop

The part most harnesses don't bother with: it's built to keep the operator's judgement
from atrophying under full delegation. Before revealing the plan it asks what chapter
flow *you* expected; the plan ships with its rejected alternatives; the completion report
proposes 15–30 minute review targets aimed at whatever the logs flagged as weak. Put
`모드: 학습` in the prompt and those touchpoints increase.

All of it is advisory and non-blocking — ignore it and the pipeline still finishes.

## Revisions

Say what's wrong in plain language and the orchestrator re-runs only the affected phase:

```
챕터 3 처음 부분이 너무 딱딱해. 다시 써줘.      → that chapter only, old draft backed up
표지를 좀 더 따뜻한 느낌으로 바꿔줘.             → cover-designer only
계획을 좀 더 입문자 친화적으로 다시 세워줘.       → Phase 2–3
```

Re-runs bump the book's minor version; the EPUB `urn:uuid` is minted once and preserved,
so readers' libraries see an update rather than a new book.

## Know before you start

- **Korean.** The harness, its prompts, and the default voice profile are Korean. Usable
  for English output, but you'll be editing the profiles.
- **Two versions, easy to confuse.** The harness version and the book version in the
  filename evolve independently.
- **Licensing splits.** Harness code is MIT; generated book content defaults to
  CC BY-NC-SA 4.0, overridable per book via `book_manifest.json`.
- **A book is a lot of tokens.** Four parallel writers plus researchers and reviewers is
  not a cheap run. Budget before you start, and consider approving a plan for a shorter
  book on the first attempt.

The mirror image of this tool is [book-to-skill](/docs/ai/book-to-skill/), which turns a
book into something an agent reads. This one turns an agent into something that writes
one.

## Next

Everything so far builds the thing. One category left, on putting it online →
[Vibe Coding Infra](/docs/vibe-infra/)

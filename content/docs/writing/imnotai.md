---
weight: 10080
title: "imnotai"
description: "Stripping the AI tells out of Korean prose without touching a single fact — 70 catalogued patterns, a change-rate hard stop, and an honest story about its own false positives."
icon: "edit_note"
date: "2026-09-10"
lastmod: "2026-09-10"
draft: false
---

Korean text written by ChatGPT, Claude, or Gemini reads *off* in a way native speakers
clock instantly and struggle to name. [imnotai](https://imnotai.kr/) names it: 10
categories and 70 sub-patterns, catalogued, severity-ranked, and rewritten span by span —
while the facts, numbers, and quotes stay byte-identical.

It comes in two forms. **[imnotai.kr](https://imnotai.kr/)** runs in a browser with
nothing to install and nothing to sign up for, for people who can't put a CLI on the
machine they're writing on. **[im-not-ai](https://github.com/epoko77-ai/im-not-ai)**
("Humanize KR") is the same taxonomy as a CLI skill, MIT-licensed, and the version you
want if you're already living in a terminal.

## Why a Korean-specific tool

QuillBot, Hix, and the other English humanizers are weak here, because Korean AI tells
mostly aren't stylistic — they're **English translationese leaking through**:

| The tell | What it should have been |
|---|---|
| AI 기술을 **통해** 효율을 높**일 수 있다** | AI로 효율을 높인다 |
| 이에 **있어서** 중요한 **점은** | 여기서 중요한 건 |
| ~**에 의해** 생성된 | ~가 만든 |
| **결론적으로**, 이는 **시사하는 바가 크다** | *(delete it)* |

The full catalogue runs from translationese (A) through mechanical parallelism —
첫째/둘째/셋째 (C), AI stock phrases (D), uniform sentence rhythm (E), hedging pile-ups
(G), sentence-initial connective spam (H), to bold-and-em-dash decoration (J). Each
pattern carries a severity: **S1** means one occurrence is already a giveaway, **S2**
tolerates one or two, **S3** only matters when it stacks with something else.

## The four rules it won't break

1. **Meaning is invariant** — facts, claims, figures, proper nouns, and direct quotes are
   preserved 100%.
2. **Evidence-based edits** — only detected spans get touched; undetected text is left
   alone.
3. **Genre holds** — a column doesn't come back as literature, a report doesn't come back
   as an essay.
4. **No over-editing** — past a 30% change rate it warns, past 50% it hard-stops.

That last one is enforced by a script with an exit code (`verify_change_rate.py`), not by
asking a model to be careful, which is the right place to put a guardrail. There's also
an explicit do-not-touch list: numbers, units, dates, proper nouns, anything inside
quotation marks, statutory text, and unavoidable academic terminology.

## Install the CLI version

```
/plugin marketplace add epoko77-ai/im-not-ai
/plugin install humanize-korean@im-not-ai
```

Then `/humanize-korean` in a new session, or just ask in plain Korean — "이 글 AI 티
없애줘", "번역투 제거" and similar phrasings all trigger it. GitHub Copilot CLI has the
same marketplace path (`copilot plugin install humanize-korean@im-not-ai`), and there's a
`./install.sh` for a cloned checkout.

One asymmetry worth knowing before you pick a host: **Copilot CLI, Codex CLI, and Gemini
CLI get the single-call path only.** The multi-call route with a separate diagnostic pass
and a finalizer is [Claude Code](/docs/ai/claude-code/) exclusive.

## Cost comes from call count, not model choice

The interesting engineering here is the router. A scoring script grades the input first
and deterministically picks one of three paths:

| Path | LLM calls | When |
|---|---|---|
| light | 1 | Already well-written — may exit early with "this is fine" |
| standard | 2 | An ordinary AI draft: diagnose once, rewrite once |
| heavy | 3+ | Dense slop, over 15,000 characters, or you asked for an audit trail |

Savings come from *making fewer calls*, not from downgrading the model — which stays your
choice. The measurement that drove the design: a 10,000-character piece run as 7 chunked
calls burned **610K tokens**; the same piece as a single call took **134K** at equal
quality. Chunking reloads the rulebook and the diagnosis for every chunk, and that
overhead eats the entire benefit. Chunking is now heavy-path only.

## The part that earned my trust

In August 2026 the data-quality firm Pebblous
[took the public code apart](https://blog.pebblous.ai/report/korean-ai-humanizer-teardown-2026-08/en/).
They confirmed the citation-tampering and over-editing guards worked — and then handed
over a reproducible counterexample: a **human essay from 2020, written before ChatGPT
existed, scored 6/6 maximum AI risk.** The cause was one author's personal comma habit
(83.3% of connective endings followed by a comma) sending a single pattern's z-score
through the roof.

Re-testing against 532 clean human documents turned a one-off into a system defect:

| Detector version | Human text misjudged "high" | medium | low |
|---|---|---|---|
| Before | **285 (53.6%)** | 171 | 76 |
| After the family cap + two-independent-signal rule | 0 | 4 | 528 |
| After genre-aware cells as well | **0** | **0** | **532 (100%)** |

More than half of human writing was being flagged, and the project published the number
rather than quietly patching it. That's the response you want from a tool that renders
judgements about whether a person wrote something.

The lesson generalises past this repo: **no single stylistic metric is evidence.** Anyone
using an AI detector as a verdict on a student, a colleague, or a job applicant should
read that teardown first.

## What it isn't

The author is direct about this, and it's worth repeating: this is **not** an "undetectable
AI" bypass. The goal is Korean writing quality — the tool improves prose that happens to
have been drafted by a model, and it is explicitly not a guarantee for academic
submission or journalistic integrity. If your institution requires disclosure, run this
and disclose anyway.

Pairs naturally with [Markdown](/docs/writing/markdown/) for drafting and
[Obsidian](/docs/writing/obsidian/) for keeping what you wrote.

## Next

Everything so far builds the thing. One category left, on putting it online →
[Vibe Coding Infra](/docs/vibe-infra/)

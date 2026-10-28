---
weight: 160700
title: "DeepTutor"
description: "HKU's agent-native learning platform — one runtime for chat, quiz, research and mastery practice, inspectable memory, and live consults with Claude Code, Codex or Grok CLI as subagents."
icon: "school"
date: "2026-10-28"
lastmod: "2026-10-28"
draft: false
---

[Resume Matcher](/docs/self-hosted/resume-matcher/) is one self-hosted app doing one job
well. [DeepTutor](https://github.com/HKUDS/DeepTutor) is the opposite shape: a whole
agent-native learning platform — chat, quiz generation, cited research, worked-problem
solving, book generation, mastery practice, and audio/video learning — sharing one
runtime and one session context, from the University of Hong Kong's Data Intelligence
Lab (HKUDS). Apache-2.0, with an [arXiv paper](https://arxiv.org/abs/2604.26962) behind
the design. Released December 2025; the project's own numbers claim 40k GitHub stars by
month nine, which is fast enough that anything in this page's feature list is a snapshot
of a moving target.

## The install path worth trying first

```text
Help me install and configure DeepTutor locally from https://github.com/HKUDS/DeepTutor.
Clone the repository, or reuse my existing checkout, and read SKILL.md and
docs-for-user/AGENT_SETUP.md first. Follow that guide to prepare the environment,
configure my model provider, verify the setup, and start the Web app.
```

Paste that into Claude Code or Codex and let it read the repo's own setup skill rather
than running commands blind. If you'd rather drive it yourself, the fastest path needs
no Python or Node at all:

```bash
docker run --rm --name deeptutor \
  -p 127.0.0.1:3782:3782 \
  -v deeptutor-data:/app/data \
  ghcr.io/hkuds/deeptutor:latest
```

Open `http://127.0.0.1:3782`, set a model provider in **Settings → Providers**. The
named volume keeps settings between runs. A PyPI install (`pip install -U deeptutor` →
`deeptutor init` → `deeptutor start`) and a from-source checkout are the other two
options — see [CONTAINERIZATION.md](https://github.com/HKUDS/DeepTutor/blob/main/docs-for-user/CONTAINERIZATION.md)
for Podman and rootless setups.

## What one runtime actually covers

Chat, Ask Questions, Quiz, Research, Visualize, Solve, Course Study, Mastery Path,
Immersive Reading, and Audio Overview all share the same capability runtime and session
state rather than being separate apps bolted together. A few of the pieces that make
that worth the setup:

- **Multi-engine knowledge base.** Versioned RAG across LlamaIndex, PageIndex, GraphRAG,
  LightRAG, or a self-hosted WeKnora base, plus linked Obsidian vaults, Kiwix ZIM
  archives, and Tencent IMA / MarginNote 4 libraries.
- **Inspectable memory.** L1 raw traces, L2 surface summaries, L3 synthesis — a Memory
  Graph links each summary back to the evidence and synthesis that produced it, so
  personalization is something you can read and edit rather than a black box.
- **Immersive reading.** A document sits beside the chat thread with page-by-page
  citations; YouTube/Bilibili links get transcript search and timestamp-grounded
  tutoring.
- **Agent-Skills ecosystem.** Skills install from **EduHub** (DeepTutor's own
  education-focused registry) or **ClawHub**, through the same open Agent-Skills format
  other tools on this site use — `deeptutor skill install socratic-tutor` or
  `clawhub:git-release-notes@1.0.1`.

## The feature that's actually rare: it can drive other agents

**My Agents** connects a live CLI — Claude Code, Codex, Grok CLI, Antigravity, Kimi,
opencode, Hermes, OpenClaw, or DeepSeek — on the same machine and consults it from inside
a chat turn via a `consult_subagent` tool. DeepTutor doesn't simulate the other agent; it
actually runs it and streams the work into the Activity panel. You can also import past
ChatGPT, Claude Code, and Codex conversation history as searchable, resumable context,
read-only — "it stays *their* conversation, not DeepTutor's own voice."

## Driving DeepTutor itself, headlessly

The CLI mirrors the web app's capabilities and adds a scripting path: `--format json`
turns any `run` into NDJSON, one event per line, each tagged with its session ID — the
same shape of "agent drives the tool" this site keeps running into (PhotoCraft's
command registry, Drift's MCP server).

```bash
deeptutor run deep_solve "Find d/dx[sin(x^2)]" --tool reason --format json

SID=$(deeptutor run deep_research "Survey 2026 papers on RAG" \
  --config mode=report --config depth=standard --format json \
  | jq -r 'select(.type=="done").session_id')
deeptutor run deep_question "Quiz me on that survey" --session "$SID" --format json
```

An `ask_user` pause with no TTY auto-resolves with an empty reply instead of hanging, so
headless runs stay headless. The repo ships a root `SKILL.md` as a compact handover doc
for exactly this — point an agent at it rather than the full README.

## The security-conscious part worth noting

Every skill import — whichever registry it came from — passes one gate before touching
your workspace: the registry's security verdict is checked first (flagged packages need
an explicit `--allow-unverified`), archives are extracted with path-traversal and
symlink checks, executable bits are stripped, and critically, **`always:` frontmatter is
stripped from a downloaded skill** — so an installed skill can never force itself into
every system prompt. Provenance (hub, version, verdict, install time) lands in
`.hub-lock.json` for later audit.

## Before you commit to it

- **This moves fast, in both directions.** Multiple point releases a week is the norm,
  not the exception — check the [release notes](https://github.com/HKUDS/DeepTutor/releases)
  for what changed since this page was written rather than trusting the feature list
  above to still be exact.
- **License and star-count claims in secondary coverage are unreliable.** Several blogs
  reported MIT instead of Apache-2.0, and star counts anywhere from 10k to 85k+ for the
  same timeframe — this page's Apache-2.0 claim comes from the repository's own
  `LICENSE` file, checked directly.
- **Tutoring-quality benchmarks in the paper use an AI student simulator**, not human
  learners, for at least some of the reported scores — read the methodology in the
  [arXiv paper](https://arxiv.org/abs/2604.26962) before citing the headline numbers.
- **It's a lot of surface area to self-host.** RAG engines, IM channel integrations,
  subagent connectors, a skills marketplace — most of that is opt-in behind extras
  (`pip install -e ".[rag-lightrag]"` and friends), but the full platform is a genuinely
  bigger operational commitment than anything else in this category.

## Next

You've been through every category. To start again, pick another from the
[overview](/docs/).

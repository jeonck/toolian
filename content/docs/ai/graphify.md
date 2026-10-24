---
weight: 51850
title: "Graphify"
description: "A knowledge-graph skill for 20+ coding assistants — tree-sitter for code, a semantic pass for docs, PDFs, images and video, every edge tagged extracted or inferred."
icon: "hub"
date: "2026-10-24"
lastmod: "2026-10-24"
draft: false
---

[Graft](/docs/ai/graft/) and [Codebase Memory MCP](/docs/ai/codebase-memory-mcp/) both
turn a repository into a graph instead of a pile of files to re-read.
[Graphify](https://github.com/Graphify-Labs/graphify) does the same job with two things
neither of those has: it maps **anything you drop in it**, not just code — PDFs,
screenshots, whiteboard photos, video — and it installs natively into 20+ assistants
rather than committing to one integration path. Apache-2.0, built by Graphify-Labs (YC
S26).

```bash
uv tool install graphifyy      # note the double y — see Naming below
graphify install               # detects your assistant, registers the skill
```

Then, inside the assistant: `/graphify .`. Three files land in `graphify-out/`:
`graph.html` (click nodes, filter, search in a browser), `GRAPH_REPORT.md` (god nodes,
communities, surprising connections), and `graph.json` (the whole graph, queryable
without re-reading your files).

## What makes an edge trustworthy

Every connection carries a tag: **`EXTRACTED`** means it was read directly from the
source — an import, a call, a citation. **`INFERRED`** means Graphify resolved it. That
distinction is the whole difference between a tool you can audit and one you have to
trust blindly, and it's rarer than it should be in this category.

```text
$ graphify explain "APIRouter"
Connections (47):
  --> RequestValidationError [uses] [INFERRED]
  --> .get() [method] [EXTRACTED]
  <-- __init__.py [imports] [EXTRACTED]

$ graphify path "FastAPI" "ModelField"
Shortest path (3 hops):
  FastAPI --uses--> DefaultPlaceholder <--references-- get_request_handler() --references--> ModelField
```

`graphify query "<question>"` scopes a subgraph to a plain-language question;
`graphify path A B` traces how two things connect; `graphify explain <node>` dumps one
node's full neighborhood with sources.

## Beyond code, specifically

Code is parsed with tree-sitter AST across roughly 40 languages — deterministic, no LLM,
nothing leaves the machine. Docs, PDFs, images, and video/audio go through a **semantic
pass**: your assistant's own model when run as a skill, or a backend you configure
(Gemini, Kimi, Claude, OpenAI, DeepSeek, Bedrock, Ollama) for headless `graphify
extract`. `# NOTE:` / `# WHY:` comments and ADR/RFC citations become first-class nodes
linked to the code they explain, rather than text the agent re-discovers every session.

`--code-only` skips the semantic pass entirely and needs no API key at all — the fully
offline path for a code-only corpus.

## Benchmarks that name their competition

Most tools in this cluster report one self-measured number. Graphify's README names the
field it's compared against, with a blind-validated second judge (90.6% agreement,
Cohen's kappa 0.81):

| Benchmark | Metric | Graphify | Field |
|---|---|---|---|
| LOCOMO (n=300) | recall@10 | **0.497** | mem0 0.048, supermemory 0.149 |
| LongMemEval-S (n=50) | QA accuracy | **76%** | tied with dense RAG |
| ERPNext cross-tool (n=6) | key-fact coverage | **82.0%** | grep/read baseline 70.8% |
| Graph build | LLM credits | **0** | per-token for most systems |

Still the maintainers' own harness and the maintainers' own choice of competitors — read
[BENCHMARKS.md](https://github.com/Graphify-Labs/graphify/blob/v8/BENCHMARKS.md) for the
reproduction commands before quoting the numbers as settled.

## Twenty-plus assistants, one install flow

```bash
graphify install                       # Claude Code, auto-detected
graphify install --platform codex      # Codex
graphify cursor install                # Cursor
graphify install --platform agents     # any Agent-Skills-spec-compliant framework
```

Also OpenCode, GitHub Copilot CLI, Gemini CLI, Kiro, Aider, Factory Droid, Trae, Devin
CLI, Google Antigravity, and more — full list behind "Pick your platform" in the README.
Hook-based platforms (Claude Code, Gemini CLI) nudge the assistant toward a graph query
automatically before a raw search; instruction-file platforms (Codex, Cursor) get the
same steering through `AGENTS.md` or `.cursor/rules/`. `--project` scopes the skill
install to the repo instead of your user profile, for committing alongside the code.

## Privacy, concretely

- **Code and video/audio**: parsed and transcribed locally (tree-sitter, faster-whisper).
  Nothing leaves the machine.
- **Docs, PDFs, images**: go to whichever model you configured — your IDE's assistant
  model for the `/graphify` skill, or an explicit backend for headless extraction.
- **No telemetry.** Queries log to `~/.cache/graphify-queries.log` for your own
  debugging (not full subgraphs); `GRAPHIFY_QUERY_LOG_DISABLE=1` turns that off too.
- `GRAPHIFY_BACKEND=none` disables every LLM backend and keeps a code-only graph, and
  `GRAPHIFY_BACKEND=ollama` keeps the semantic pass local as well, for data-residency
  requirements code-only mode can't satisfy on its own.

## Naming — read this before you install

**The official package is `graphifyy`, with a double y.** Other `graphify*` packages on
PyPI aren't affiliated with the project. The CLI command itself is still `graphify`.

Worse than a naming collision: secondary "installer" listings for a
**"Graphify Desktop 2026"** circulate with a cracked "Patch Activator Keygen" and a
"License Key Pre-Activated" — the standard shape of a malware lure, with no connection to
this project. Install only from `uv tool install graphifyy`, `pipx install graphifyy`,
or the [official repo](https://github.com/Graphify-Labs/graphify). Anything offering a
license-key bypass for an open-source, Apache-2.0 tool is lying about what it is.

## The free CLI sits in front of a paid product

Graphify-Labs is YC-backed, and the README leads with a 14-day trial for **Graphify
Cloud** — always-on, cross-repo graphs, formal verification, and Sentry/Jira connectors
for teams who outgrow a per-repo local run. The CLI covered on this page is the free,
local, Apache-2.0 half; the cloud product is the commercial half funding it. Neither
fact should change which one you reach for — a single repo on your own machine is
squarely what the free CLI is for — but it's the same pattern as
[promptfoo](/docs/ai/promptfoo/) and [Langfuse](/docs/ai/langfuse/): open-source tooling
with a venture-backed company monetizing the layer above it.

## Next

Specialists instead of a generalist → [Agency Agents](/docs/ai/agency-agents/)

---
weight: 5060
title: "Codebase Memory MCP"
description: "Indexing a repository into a queryable graph an agent can ask structural questions of, instead of grepping."
icon: "schema"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

"Who calls this function?" is a question with an exact answer, and an agent that answers
it by reading files will spend tens of thousands of tokens approximating one.
[Codebase Memory MCP](https://github.com/DeusData/codebase-memory-mcp) indexes the
repository into a persistent graph and exposes it over MCP, so the agent asks the
question and gets the answer in a millisecond.

The maintainers' headline comparison: five structural queries costing ~3,400 tokens
through the server against ~412,000 tokens exploring file by file — a **99.2%
reduction** — and the Linux kernel, 28M lines, indexed in three minutes. Their numbers,
their benchmark; the shape of the saving is what to take from it.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash
```

It's a native binary — no runtime, no API key, no Docker — and the installer detects
your MCP clients and writes their config for you. If piping a script into a shell isn't
something you do (reasonable), the project also publishes on npm, PyPI, Homebrew, Scoop,
and Winget, and ships plain binaries on the releases page. Read the script first if you
do run it; that advice applies to every `curl | bash` you meet.

Windows uses the PowerShell equivalent:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.ps1 -OutFile install.ps1
Unblock-File .\install.ps1
.\install.ps1
```

## First run

Point it at a repository and let it index:

```
> Index this repository with codebase memory, then show me the architecture overview
```

The index lands in `~/.cache/codebase-memory-mcp/` as SQLite, so it survives restarts
and costs nothing to keep. `CBM_CACHE_DIR` moves it elsewhere.

## What the agent gets

Fifteen tools, in three groups:

| Group | Tools |
|---|---|
| Indexing | `index_repository`, `index_status`, `list_projects`, `delete_project` |
| Querying | `search_graph`, `search_code`, `get_code_snippet`, `query_graph`, `get_graph_schema` |
| Analysis | `trace_path`, `get_architecture`, `detect_changes`, `manage_adr`, `ingest_traces` |

`trace_path` — call chains between two symbols — and `get_architecture` are the ones that
change how a session feels. Instead of "read these twelve files and infer the flow", the
agent asks for the path and starts working.

Coverage is 158 languages through vendored tree-sitter grammars, with semantic type
resolution via LSP for a dozen of the common ones, plus Dockerfiles and Kubernetes
manifests. Everything runs locally; the code never leaves the machine.

## Graft or this?

They solve the same problem from opposite ends, and both are MIT licensed.

| | [Graft](/docs/ai/graft/) | Codebase Memory MCP |
|---|---|---|
| Output | Markdown nodes in your repo | SQLite graph in a cache directory |
| Readable by you | Yes — it's documentation | Only through queries |
| Languages | ~20, four at full fidelity | 158, twelve with semantic types |
| Optional LLM pass | Yes (`--deep`) | No — purely structural |
| Best at | Orientation and architecture prose | Precise structural questions at scale |

Pick Graft if you want something you can read and review; pick this if the repository is
huge and the questions are mechanical. Running both is not absurd — they don't conflict —
but start with one.

## Cautions

- **Reindex after big changes.** `detect_changes` tells you when the graph has drifted;
  a stale answer delivered confidently is the failure mode of every index.
- **It answers structure, not intent.** Nothing here knows *why* the code is like that.
- **Check the install path in your threat model.** Local binary, local index, no network
  calls for queries — but it is a third-party binary with access to your source.

## Next

Specialists instead of a generalist → [Agency Agents](/docs/ai/agency-agents/)

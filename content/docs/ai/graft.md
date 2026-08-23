---
weight: 5050
title: "Graft"
description: "A pre-built map of your repository so the agent stops re-reading the same files on every task."
icon: "account_tree"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Watch a coding agent start a task and you'll see the same opening every time: list the
directory, grep for a symbol, open six files, work out how they relate. It is thorough,
it is slow, and you pay for all of it — again on the next task, in the same repository.

[Graft](https://github.com/NanoNets/Graft) does that exploration once. It parses the
repository with tree-sitter into a graph of markdown nodes — subsystems, APIs, concepts —
with typed links between them, and wires the agent to read the graph instead of the
files. The project reports **3× faster and 4× cheaper** sessions, and on SWE-bench
Verified 66% correctness against a 54% baseline with 23% fewer tokens. Treat those as
the maintainers' benchmark rather than a guarantee; the mechanism is sound either way.

## Install

```bash
npm install -g @nanonets/graft
cd ~/projects/my-app
graft init      # asks which agents to wire, then builds the graph
```

`init` writes a `graft/` directory and adds it to `.gitignore` — the graph is a cache,
regenerable from source, not something to commit. For Claude Code it also installs a
skill at `.claude/skills/graft/SKILL.md` without overwriting your existing settings.

## The commands

| Command | What it does |
|---|---|
| `graft build` | Build the structural graph with tree-sitter — no API key, no cost |
| `graft build --deep` | Add LLM-written summaries and concept nodes |
| `graft ask "<task>"` | Return the nodes and files relevant to a task |
| `graft grep "<regex>"` | Search, grouped by enclosing symbol rather than by line |
| `graft map` | Directory clusters, hubs, and hotspots — orientation in a new repo |
| `graft check` | Report where the graph has drifted from the code |
| `graft viz` | Open the graph in an interactive view |

The split that matters: **`build` is free and deterministic**, `--deep` calls a model.
You can run Graft indefinitely without an API key and still get the structure; the deep
pass buys prose summaries on top.

`graft ask` is worth running yourself, not just letting the agent call it. It is the
fastest way to answer "where does this actually live" in a repository you didn't write.

## With Claude Code

Choose Claude Code during `init` and you get context injection on each prompt, a
statusline showing graph size and staleness, an automatic structural refresh after edits,
and a blast-radius view of what depends on the file you just changed. Graft also
registers an MCP server exposing the same operations as tools — `graft_find_code`,
`graft_file_api`, `graft_trace_calls`, `graft_repo_map`, and friends — so any MCP-capable
agent can use the graph.

## Language coverage

| Fidelity | Languages |
|---|---|
| Full, scope-aware with cross-file resolution | TypeScript/JavaScript, Python, Go, Java |
| Symbols and generic call edges | Rust, C, C++, C#, Ruby, PHP, Kotlin, Scala, Swift, Elixir, Dart, and more |
| Compiler-grade, opt-in `--lsp` | Anything with a language server — rust-analyzer, clangd, gopls, pyright |

Monorepos build one graph and scope answers per package (`graft ask "..." --in apps/api/`);
submodules come along with `--follow-submodules`.

## Worth knowing before you adopt it

- **A stale graph is worse than none.** Run `graft check` after a big merge; the auto-sync
  covers ordinary edits, not a rebase that moved half the tree.
- **Deep mode costs tokens.** Building summaries for a large repository is a real bill.
  Start structural, add `--deep` only if the agent still gets lost.
- **Telemetry is on by default.** Anonymous usage buckets, no code or paths, and
  `graft telemetry disable` or `DO_NOT_TRACK=1` turns it off.
- MIT licensed, and the graph lives in your repository — nothing to migrate if you stop.

## Next

For the same idea as an MCP server with a database behind it →
[Codebase Memory MCP](/docs/ai/codebase-memory-mcp/)

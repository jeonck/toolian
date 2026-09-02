---
weight: 5055
title: "Luvus"
description: "A terminal mission control that runs, watches, resumes, and orchestrates several AI coding agents in one place."
icon: "dashboard"
date: "2026-09-01"
lastmod: "2026-09-01"
draft: false
---

Once you run more than one agent — Claude Code in one window, Codex in another, a
third waiting on a question you never saw — the bottleneck stops being the agents and
becomes **you**, alt-tabbing between panes. Luvus is a terminal UI that puts them all
on one screen.

## Install and run

```bash
curl -fsSL https://luvus.dev/install.sh | sh
# or
brew install RizRiyz/luvus/luvus
```

```bash
cd ~/projects/my-app
luvus
```

## Agents it drives

Claude Code, Codex, Gemini CLI, Aider, opencode, GitHub Copilot, Cursor, Amp, Droid,
Grok, Kimi, Qwen, Pi — and **Kiro**. It doesn't replace them; it launches the CLI you
already use and keeps the session in a pane.

## What it actually buys you

| Problem | What Luvus does |
|---|---|
| An agent stalled on a prompt and you didn't notice | Status per pane — working, blocked, idle |
| No idea what a session cost | Token and cost tracking per project |
| Terminal closed, context gone | Sessions resume on restart |
| Want to try a second approach | Fork a session into a new pane, history intact |
| Agents needing to hand work over | Message another pane with `=` |
| Two agents editing the same files | Isolated worktrees and path leases |

## The keys worth knowing

| Key | Action |
|---|---|
| `⌃Space` | Prefix for commands |
| `⌃Space f` | Fork the session into a new pane |
| `=` + pane name | Send a task to another agent |
| `j` / `k` | Move between panes |
| `/` | Filter |
| `q` | Close |

## Review and git in place

Diffs are reviewed inside Luvus: leave notes on the change and send them straight back
to the running agent, instead of pasting file paths into a chat. Commits, branches,
PRs, and issues go through the same window — it leans on `git` and the `gh` CLI, so
have both installed.

## Working remotely

Sessions attach over SSH with little bandwidth, which means a long-running agent can
live on a desktop or server and you can check on it from a laptop — or from a terminal
app on a phone.

## When it's overkill

One agent, one repo, one task at a time — a plain terminal is fine. Luvus starts to
pay off at two or three concurrent agents, and clearly pays off when tasks have
dependencies and need to stay out of each other's files.

## Next

Whichever agents you run, they work better with a map of the repository →
[Graft](/docs/ai/graft/)

---
weight: 5036
title: "Muse Code"
description: "Meta's terminal coding agent — persistent subagents in isolated worktrees, rewind on double-Esc, a single static binary — and a default billing tier that trains on your code."
icon: "terminal"
date: "2026-09-12"
lastmod: "2026-09-12"
draft: false
---

[Muse Code](https://developer.meta.com/ai/) is Meta Superintelligence Labs' answer to
[Claude Code](/docs/ai/claude-code/): a terminal-only agent, released in beta on **5
August 2026**, running on Muse Spark — a coding model co-trained with the harness rather
than bolted to it afterwards.

It is a credible first entry, and it has one setting that can cost you your source code if
you install it and start working. That section is below, and it's the reason to read this
page before running the install line.

## Install

```bash
curl -fsSL https://dev.meta.ai/install.sh | bash
muse-code auth
```

The binary is statically linked: the installer needs only `curl`, `mktemp` and bash — no
Node, no Python, no Homebrew. `git` isn't needed to install but is required at runtime,
because the worktree features are built on it. Auth is an OAuth browser flow, with a
device-code URL if the browser doesn't open.

| Platform | Status |
|---|---|
| macOS 12+ | Supported, x86_64 and arm64 |
| Linux (Ubuntu 20.04+, Debian, Fedora) | Supported |
| Windows | **No native support** — install inside WSL2 |

As with any `curl … | bash`, you're executing a remote script with your user's
privileges, sight unseen. Fetch it to a file, read it, then run it — the same two
extra seconds you'd want from any vendor.

## Check your billing tier before you point it at work code

Muse Code is sold as plans (Everyday $5, High $15, Power $50) or pay-as-you-go through the
Meta Model API. Two token rates matter:

| Tier | Input / 1M | Cached input | Output / 1M | The deal |
|---|---|---|---|---|
| Standard | $1.25 | $0.15 | $4.25 | Ordinary paid API terms |
| **Contributor** | **$0.10** | $0.002 | **$0.20** | **Meta may train on your prompts and completions** |

That's roughly 20× cheaper, and the price is your sessions — prompts, completions, and
therefore whatever code you were working on. Reporting on the beta says the CLI **lands on
the Contributor tier after install**, so opting out is something you do, not something
that happens by default.

Whether or not that default holds in your install, the action is the same and takes a
minute: **confirm the tier before the first run against a private repository.** For client
work or anything under an NDA, the cheap tier isn't a trade-off you're allowed to make.

## What's actually new in it

**Persistent subagents.** Rather than spawning a fresh helper per task and re-gathering
the same context every time, subagents stay alive for the session, work in **isolated git
worktrees**, and decide for themselves when to report back. The worktree isolation is the
part worth stealing conceptually — parallel agents that can't stomp on each other's
files.

**Workflow** orchestrates whole teams of those subagents against one large task, reviewing
or building in parallel while you watch and steer from a single view.

**Rewind** — double-Esc — rolls the session back to an earlier point in the conversation.
Cheap to use and the fastest fix when an agent has talked itself into a corner.

Underneath, a crash-safe event log keeps long-horizon repository work recoverable, and
there's inter-session messaging plus an SDK in developer preview.

## Slash commands, with one gotcha

`/goal` is a real slash command. Most of the things people *call* commands — `plan`,
`grill` — are **skills**, invoked as `/skill plan` and `/skill grill`. The built-in skill
ids are `create-skill`, `doctor`, `git`, `grill`, `grill-and-record`, `import`,
`manage-settings`, `plan`, `read-session` and `taste`. If `/plan` returns nothing useful,
that's why.

## Is it worth switching?

Not yet, and not as a replacement. It's a beta, the benchmark numbers are vendor-reported,
and the ecosystem around [Claude Code](/docs/ai/claude-code/) — skills, plugins, MCP
servers — is years ahead. What Muse Code brings that's genuinely its own is the
worktree-isolated persistent subagent model and a single static binary with no runtime to
install.

Worth an afternoon on a scratch repository, on the Standard tier. Not worth pointing at
your employer's monorepo this week.

## Next

When code can't leave your machine → [Ollama](/docs/ai/ollama/)

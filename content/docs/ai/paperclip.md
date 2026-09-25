---
weight: 5190
title: "Paperclip"
description: "An org chart, ticket system, and budget ceiling for a fleet of AI agents — the control plane for when twenty Claude Code tabs stop being manageable."
icon: "hub"
date: "2026-09-25"
lastmod: "2026-09-25"
draft: false
---

Every tool up to here makes *one* agent better. [Paperclip](https://github.com/paperclipai/paperclip)
is for the point after that — when you have a dozen agents running, no idea which tab is
doing what, and a token bill that surprised you. Its own framing is the clearest one:
if a coding agent is an *employee*, Paperclip is the *company*.

It's a Node.js server with a React UI and an embedded Postgres, MIT licensed, self-hosted,
no account required.

## What it actually gives you

| Without | With |
|---|---|
| 20 terminal tabs; a reboot loses all of it | Ticket-based tasks, threaded conversations, sessions that survive restarts |
| Re-pasting context so the agent remembers the project | Context flows task → project → company goal, so every task carries its "why" |
| A folder of agent configs and hand-rolled coordination | Org chart, delegation, approvals, governance out of the box |
| A runaway loop burning hundreds of dollars before you notice | Per-agent monthly budgets that throttle the agent when hit |
| Remembering to kick off recurring jobs | Heartbeats: agents wake on a schedule, check work, act |

The four things it's built around are tasks, the org, training, and the runtime — an
agentic task manager, an org chart with roles and scoped secrets, a skill studio with
evals, and the infrastructure underneath (sandboxing, MCP servers, RBAC, cost controls).

## Bring your own agent

Nothing here is model- or vendor-specific. Claude Code, Codex, Cursor, OpenClaw, a bash
script, an HTTP webhook — "if it can receive a heartbeat, it's hired." That's the reason
it belongs in this category rather than next to a single vendor's tooling: it sits
*above* whatever you're already running.

## Install

```bash
curl -fsSLO https://paperclip.ing/install.sh
curl -fsSLO https://paperclip.ing/install.sh.sha256
shasum -a 256 -c install.sh.sha256      # sha256sum -c on Linux
bash install.sh
```

The installer ensures Node.js 24.11+, puts a managed CLI under `~/.paperclip/cli`, runs
interactive onboarding, and can register a background service on Linux and macOS. Note
the checksum is served from the same origin as the script, so it catches transfer
mistakes, not a compromised origin — pin to a release tag from GitHub if that matters.

Non-interactive:

```bash
curl -fsSL https://paperclip.ing/install.sh | bash -s -- --no-prompt --no-onboard
paperclipai onboard --yes
```

Just to look around, without touching anything:

```bash
ANTHROPIC_API_KEY=... npx paperclipai test-drive
```

## The orchestration details it gets right

These are the parts that are tedious to build yourself and easy to get subtly wrong:

- **Atomic checkout.** Task claiming and budget enforcement happen atomically, so two
  agents can't pick up the same ticket and a budget can't be blown past by a race.
- **Persistent state across heartbeats.** An agent resumes its task context instead of
  restarting cold every wake-up.
- **Governance with rollback.** Approval gates are enforced, config changes are
  revisioned, bad ones roll back.
- **Multi-organization isolation.** Every entity is company-scoped, so one deployment
  runs several companies with separate data and audit trails.
- **Portable company templates.** Export and import orgs, agents, and skills, with
  secrets scrubbed.

## Be honest about the scale

This is heavy machinery. Org charts and approval gates for a single agent working on one
repo is pure overhead — [Claude Code](/docs/ai/claude-code/) with a `CLAUDE.md` is the
right answer there, and stays right for a long time.

The threshold is roughly: several agents, running unattended, on work you can't watch
live, with a token budget you care about. Below that, you're administering software
instead of shipping. Above it, you're hand-rolling a worse version of this.

Also worth saying plainly: agents running autonomously against real systems can do real
damage. The approval gates and budgets exist for a reason — configure them before you
hand anything the keys, not after.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)

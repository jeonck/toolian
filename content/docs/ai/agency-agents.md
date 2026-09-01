---
weight: 5080
title: "Agency Agents"
description: "A library of 230+ ready-made subagent definitions across seventeen divisions — and why you should install about six of them."
icon: "groups"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

A subagent is a scoped role you hand a piece of work to: its own instructions, its own
tools, its own slice of context. Writing a good one takes a couple of hours of iterating
on the prompt. [Agency Agents](https://github.com/msitarzewski/agency-agents) is a
library of **230+ of them already written**, organised into seventeen divisions —
Engineering, Design, Testing, Security, Product, Marketing, Finance, Game Development,
GIS, Healthcare, and more.

Each agent file is more than a persona line: front matter with identity and mission,
critical rules, a worked process, concrete deliverables with examples, and success
metrics. That structure is the actual value — it's the part people skip when they write
their own.

## Install

The repository ships an installer that detects which tools you have and lets you choose
by division or by individual agent:

```bash
git clone https://github.com/msitarzewski/agency-agents.git
cd agency-agents
./scripts/install.sh --tool claude-code
```

Run it with no flags for the interactive picker. For editors other than Claude Code,
convert first:

```bash
./scripts/convert.sh
./scripts/install.sh
```

Claude Code, Copilot, Cursor, Windsurf, Aider, Gemini CLI, OpenCode, Codex and others are
supported. There's also a desktop app if you'd rather not touch the terminal. MIT
licensed, so adapting an agent for your team is fine.

Claude Code reads agents from `~/.claude/agents/` (yours) and `.claude/agents/` (the
project's) — the installer writes to the first; copy the handful your team shares into
the second and commit them.

## Use one

```
> Use the Frontend Developer agent to review this component for accessibility
> Have the Security Auditor agent look at the auth middleware
```

The agent runs with its own context, so a long review doesn't crowd out your main
session — which is the practical reason to bother, quite apart from the expertise in the
prompt.

## Install six, not two hundred

This is the part the star count won't tell you. A wall of 230 agents makes every
selection worse: you can't remember which exist, the picker becomes noise, and an agent
you never invoke is a file that will drift out of date. OpenCode makes the point
concretely — its runtime registers roughly 119 agents and **silently drops the rest**, so
targeting it means `--division` flags whether you like it or not.

A workable approach:

1. Install one division you actually work in — Engineering, say.
2. Use it for a week and notice which three or four agents you invoke by name.
3. Uninstall the rest, and copy the survivors into the project's `.claude/agents/` so
   your team gets them too.
4. **Edit them.** These are starting points; the version that knows your stack, your test
   command, and your conventions is worth five generic ones.

## Read before you run

The agent definitions are prompts from a third party, and a prompt is instructions your
agent will follow. Skim the ones you install — particularly any that tell the agent to
run commands, install packages, or reach the network. That's not a knock on this project;
it's the correct habit for every prompt library you install.

## Next

The most unexpected thing on this list — an agent that edits video →
[OpenMontage](/docs/ai/openmontage/)

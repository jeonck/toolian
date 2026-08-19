---
weight: 5010
title: "Claude Code"
description: "An agentic coding tool that reads a whole repository and edits files from the terminal."
icon: "smart_toy"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Claude Code is not autocomplete — it's something you **hand a task to**. "Find and fix
the cause of this bug", "add tests for this folder": work that spans several files, it
explores and edits on its own.

## Install and run

```bash
npm install -g @anthropic-ai/claude-code
cd ~/projects/my-app
claude
```

Besides the terminal, it's available as a desktop app (Mac/Windows), on the web
(claude.ai/code), and as VS Code and JetBrains extensions.

## What it suits

| Good fit | Poor fit |
|---|---|
| Refactoring across many files | Single-line completion (Copilot is faster) |
| Getting oriented in an unfamiliar repo | Precise algorithms with one right answer |
| Writing tests and chasing failures | Design work where requirements aren't settled |
| Repetitive migration work | Irreversible production operations |

## The basic loop

```
> Summarise the authentication flow in this project
> Raise test coverage in src/auth, focusing on failure cases
> Commit what you just changed
```

Give instructions in plain language; it reads files, shows a plan, and edits after you
approve. Because edits are confirmed first, you can decline anything you didn't want.

## Common commands

| Command | Action |
|---|---|
| `/help` | List available commands |
| `/clear` | Reset conversation context (when the topic changes) |
| `/config` | Change the model and settings |
| `/init` | Analyse the repo and generate `CLAUDE.md` |
| `Esc` | Interrupt what it's doing |
| `Ctrl+C` twice | Quit |

## Supplying context with CLAUDE.md

`CLAUDE.md` at the repository root is read automatically every session. Put your team's
rules there and you stop repeating yourself.

```markdown
# Project rules

- The package manager is pnpm. Do not use npm commands.
- Tests: `pnpm test`. They must pass before committing.
- API response types live only in src/types/api.ts.
- Never edit migration files; create new ones.
```

## Getting good results

- **Cut the work small.** "This module first" beats "refactor everything."
- **Tell it how to verify.** Give it the test or lint command and it will check its own
  work and fix what broke.
- **Use `/clear` often.** Leftover context from an earlier topic sends it into the
  wrong files.
- **Let git be the safety net.** Commit before you start and you can always back out.

## A caution

Always **read and test** what it generates. For anything hard to reverse — deletions,
deploys, database migrations — verify yourself before running it.

## Next

If you want suggestions as you type → [GitHub Copilot](/docs/ai/copilot/)

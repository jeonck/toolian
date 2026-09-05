---
weight: 5056
title: "Orca"
description: "A desktop IDE that gives each agent task its own git worktree, terminal, browser tab, and diff to review."
icon: "grid_view"
date: "2026-09-04"
lastmod: "2026-09-04"
draft: false
---

Orca answers the same problem as [Luvus](/docs/ai/luvus/) — several agents working at
once — from the other direction. Instead of a terminal UI, it's a desktop application,
and the unit isn't a pane but a **task**: each one gets its own git worktree, its own
agent terminal, its own browser tab, and its own diff.

## Install

```bash
brew install --cask stablyai/orca/orca
```

Windows has an installer and Linux an AppImage, both on the
[releases page](https://github.com/stablyai/orca/releases). There's a companion mobile
app for checking on a run from a phone.

It drives the agent CLIs you already have — Claude Code, Codex, Cursor CLI, GLM — so
you still need whatever subscription those require. Orca itself is MIT-licensed and
free.

## Why worktrees are the point

Two agents editing one checkout is how you get a merge conflict with yourself. Orca
gives each task `git worktree` isolation, so branches move independently and nothing
half-finished blocks the next task.

```bash
# what Orca is doing for you, by hand
git worktree add ../myapp-feature-a -b feature-a
git worktree add ../myapp-bugfix-b -b bugfix-b
git worktree list
```

When the work is merged or abandoned, the worktree goes with it.

## The panels

| Panel | What it's for |
|---|---|
| Terminal | The agent's session, one per task |
| Browser | The dev server for *that* worktree, not a shared localhost |
| Diff | Review changes and annotate them before anything is committed |
| Layouts | Tabs and split panes, so two tasks sit side by side |

The browser panel matters more than it sounds: with several worktrees running, each
needs its own port, and having the right preview attached to the right task removes a
constant source of "why didn't my change show up."

## Running it elsewhere

Tasks can execute over SSH or on a self-hosted server, which keeps a long build off
your laptop while the review still happens in the local window.

## Luvus or Orca

| | [Luvus](/docs/ai/luvus/) | Orca |
|---|---|---|
| Shape | Terminal UI | Desktop app |
| Works over plain SSH | Yes | Local window, remote execution |
| Isolation | Worktrees and path leases | A worktree per task |
| Review | Diff with notes in the TUI | Diff panel with annotations |
| Fits if | You live in tmux | You want a preview browser beside the diff |

Both wrap the same agent CLIs, so trying one costs nothing but disk.

## One gotcha on Linux

`orca` is also the GNOME screen reader. On a Linux desktop, running bare `orca` in a
terminal may start speech instead of the IDE — use the AppImage or the distribution's
own launcher and confirm which binary you're calling.

```bash
which -a orca
```

## Next

Whichever agents you run, they work better with a map of the repository →
[Graft](/docs/ai/graft/)

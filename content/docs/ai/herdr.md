---
weight: 5057
title: "herdr"
description: "tmux rebuilt for the era of several coding agents at once — every pane marked working, blocked or idle, and a socket API the agents themselves can drive."
icon: "dashboard"
date: "2026-09-20"
lastmod: "2026-09-20"
draft: false
---

Run four agents at once and the bottleneck stops being the agents. It becomes you,
cycling through panes to find which one stopped three minutes ago waiting for a yes.

tmux can't help: it was built before any of this existed and has no concept of what's
happening inside a pane. [herdr](https://github.com/herdrdev/herdr) is the same idea with
that concept added — a single Rust binary, Apache-2.0, running in whatever terminal you
already use.

## Install

```bash
curl -fsSL https://herdr.dev/install.sh | sh
herdr
```

Also `brew install herdr` or `mise use -g herdr`, which are the better options if you'd
rather not pipe a remote script into a shell. Windows gets a PowerShell installer, and
there are plain binaries on the releases page.

Start it where the work lives, run your agents, split panes, walk away. `ctrl+b q`
detaches; `herdr` reattaches. If tmux is in your fingers, so is this — and the mouse
works too, click, drag and split, which tmux users spend years pretending they don't
want.

## Never hunt for the stuck one

Every pane carries a state: **working, blocked, or idle.** When an agent stops and needs
an answer, the sidebar says so rather than leaving you to notice. That single feature is
most of the value — supervising parallel agents is an interrupt-driven job, and
everything else is a way of not missing the interrupt.

It runs what you already run — Claude Code, Codex, Cursor, opencode, Grok and the rest —
and importantly **doesn't wrap or replace them.** It owns their terminals. Nothing about
your agent setup changes.

## What "survives a disconnect" actually means

A background server holds the terminals, so closing the client or losing SSH doesn't stop
the work. Worth being precise about the limit, because summaries of this tool routinely
overstate it:

> After a **server or machine restart**, herdr restores the saved layout and can resume
> supported agent sessions — but **the original processes do not survive.**

So: drop your connection, close the lid, reattach from another machine — fine, the work
kept running. Reboot the box — you get your layout back and supported agents resume, not
the same processes carrying on. Plan long jobs accordingly.

Several machines can also live in one window: local work alongside saved SSH machines,
one combined agent list, reconnecting independently.

## The part that isn't just a nicer tmux

herdr exposes a **CLI and a JSON socket API that agents drive themselves.** An agent can
spawn a pane, start another agent in it, prompt it, and wait until that one is *genuinely
blocked* rather than polling for output that may never come.

That turns the multiplexer into coordination infrastructure. The interesting version
isn't you watching four agents — it's one agent fanning out work to three others and
being told, by the runtime, exactly when each needs something. There's a plugin
marketplace on top of that for extending panes and workflows.

## Against the other two here

| | Best at |
|---|---|
| [Luvus](/docs/ai/luvus/) | A terminal mission control that runs, watches and resumes agents for you |
| [Orca](/docs/ai/orca/) | A desktop app giving each task its own worktree, terminal, browser and diff |
| **herdr** | The layer underneath: it owns the terminals, and the agents can program it |

Orca is the one to pick if you want isolation per task and a GUI to review diffs in.
herdr is the one to pick if your work already lives in a terminal over SSH and you want
the agents themselves to be able to coordinate.

It hit #1 on GitHub Trending in June 2026 and raised a seed round in September — fast
enough that the version you install will have moved past this page. Check the docs for
anything that matters.

## Next

Whichever agents you run, they work better with a map of the repository →
[Graft](/docs/ai/graft/)

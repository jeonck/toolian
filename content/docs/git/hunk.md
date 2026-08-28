---
weight: 6070
title: "hunk"
description: "A terminal diff viewer built for reviewing changes you didn't write — including the ones an agent wrote."
icon: "difference"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Reviewing a diff in a pager works while the diff is small and yours. It stops working
when the change spans fifteen files and you didn't write any of it — which is now a
normal Tuesday, because an agent wrote it and you're the reviewer.

[hunk](https://hunk.dev/) is a terminal UI built for exactly that: a sidebar of the
changed files, one pane of the diff, keyboard and mouse navigation, and a layout that
adapts to your terminal width. It reads Git, and also Jujutsu and Sapling.

## Install

```bash
npm install -g hunkdiff
brew install hunk
curl -fsSL https://hunk.dev/install.sh | sh
mise use -g hunk
```

MIT licensed. Node 18+ if you install from npm; the other paths ship a binary.

## Use it

```bash
hunk diff                     # working-tree changes
hunk diff --watch             # re-read as files change
hunk show                     # the last commit
hunk show HEAD~1              # any commit
hunk diff before.ts after.ts  # two files, no repository needed
git diff --no-color | hunk patch -
```

The habit that makes it stick is making it Git's pager:

```bash
git config --global core.pager "hunk pager"
```

Now `git diff`, `git show`, and `git log -p` all open in it, and you don't have to
remember a new command to get the better view.

`--watch` is the one to try first. Leave `hunk diff --watch` open in a split while an
agent works, and the review updates itself as files land — you're reading the change as
it happens instead of facing all of it at the end.

## Configure it once

`~/.config/hunk/config.toml`, or `.hunk/config.toml` to set it per project:

```toml
theme = "github-dark-default"   # or "auto" to follow the terminal
mode = "auto"                   # auto | split | stack
vcs = "git"                     # git | jj | sl
watch = false
line_numbers = true
wrap_lines = false
sidebar = "auto"
```

`mode` is worth setting deliberately: `split` for a wide monitor where side-by-side
helps, `stack` on a laptop where side-by-side halves your usable columns.

## One thing it doesn't do

It reviews; it does not stage. There's no "stage this hunk" key — for building a commit
out of parts of your working tree, that's still `git add -p`, or
[lazygit](/docs/git/lazygit/), which does it with a keystroke per hunk. hunk's job ends
at understanding the change.

That division is the honest way to fit it into the workflow:

| Want | Tool |
|---|---|
| Read a large change carefully | hunk |
| Prettier `git diff` inside your existing pager habits | [delta](/docs/git/delta/) |
| Stage parts of a file, then commit | [lazygit](/docs/git/lazygit/) or `git add -p` |

delta and hunk overlap and you don't need both — delta is a syntax-highlighting pager
that improves every diff you already look at; hunk is a place you go to *review*, with
file navigation and layout control. Try hunk when the diffs you read are big and
someone else's.

## Next

Catch problems before the diff is even written →
[pre-commit hooks](/docs/git/pre-commit/)

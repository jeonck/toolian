---
weight: 6040
title: "delta"
description: "A pager that adds syntax highlighting, line numbers, and side-by-side view to git diff."
icon: "difference"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Plain `git diff` colours whole lines red and green. Change one word and the entire line
lights up, leaving you to hunt for the difference by eye. `delta` **highlights only
what changed** and adds syntax colouring on top.

## Install

```bash
brew install git-delta
sudo apt install git-delta
winget install dandavison.delta
```

The package is `git-delta`; the binary is `delta`. Take care not to confuse it with
other packages named `delta`.

## Configuration

```bash
git config --global core.pager delta
git config --global interactive.diffFilter 'delta --color-only'
git config --global delta.navigate true
git config --global delta.line-numbers true
git config --global merge.conflictStyle zdiff3
```

Or directly in `~/.gitconfig`:

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    line-numbers = true
    side-by-side = false
    syntax-theme = Monokai Extended

[merge]
    conflictStyle = zdiff3
```

## Usage

Once configured, your existing commands route through it.

```bash
git diff
git show HEAD
git log -p
```

With `navigate = true`, `n` and `N` jump file to file inside the pager — a real help on
diffs that touch a lot of files.

## Side-by-side

```bash
git diff --side-by-side       # or set delta.side-by-side = true as the default
```

Side-by-side reads well on a wide monitor; the default stacked view is better on a
laptop. To toggle per situation, keep an alias around.

```bash
alias gds='git -c delta.side-by-side=true diff'
```

## Elsewhere

```bash
# compare two arbitrary files
delta old.json new.json

# as the lazygit pager (config.yml)
# git.paging.pager: delta --dark --paging=never
```

## Themes

```bash
delta --list-syntax-themes          # available themes
delta --show-config                 # current settings
```

Pass `--light` or `--dark` to match your terminal.

## Next

Catch problems before the diff is even written →
[pre-commit hooks](/docs/git/pre-commit/)

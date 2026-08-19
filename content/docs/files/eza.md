---
weight: 3040
title: "eza"
description: "An ls replacement with colour, icons, tree view, and Git status."
icon: "list"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`ls -alh` gives you plenty of information and very little structure. `eza` presents the
same data with colour and alignment, and adds tree view and Git status on top.

## Install

```bash
brew install eza
sudo apt install eza            # Ubuntu 24.04+
cargo install eza               # elsewhere
```

## Basics

```bash
eza                    # plain listing
eza -l                 # long form (permissions, size, mtime)
eza -la                # include hidden files
eza -l --git           # add a Git status column
eza --tree --level=2   # two levels of tree
eza -l --sort=modified --reverse   # most recently changed first
```

## Options worth knowing

| Option | Meaning |
|---|---|
| `-l` | Long listing |
| `-a` | Include hidden files |
| `-T` / `--tree` | Tree view |
| `--level=N` | Tree depth |
| `--git` | Git status column |
| `--icons` | File-type icons (needs a Nerd Font) |
| `--group-directories-first` | Directories on top |
| `-s size` / `-s modified` | Sort key |
| `--total-size` | Compute real directory sizes |

## Suggested aliases

```bash
# ~/.zshrc
alias ls='eza --group-directories-first'
alias ll='eza -l --git --group-directories-first'
alias la='eza -la --git --group-directories-first'
alias lt='eza --tree --level=2 --group-directories-first'
```

If overriding `ls` makes you nervous, alias only `l` and `ll` and leave `ls` alone —
scripts that parse `ls` output can break.

## Instead of tree

```bash
eza --tree --level=3 --ignore-glob="node_modules|.git"
```

Handy when you want to paste a directory structure into a document.

## Next

From text to JSON → [jq](/docs/files/jq/)

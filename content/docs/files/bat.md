---
weight: 3030
title: "bat"
description: "A cat replacement with syntax highlighting, line numbers, and Git change markers."
icon: "description"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`cat` on a 200-line config file dumps a monochrome wall of text. `bat` shows the same
file with syntax highlighting, line numbers, and paging.

## Install

```bash
brew install bat
sudo apt install bat            # the binary is called batcat
winget install sharkdp.bat
```

```bash
alias bat=batcat                # Ubuntu
```

## Basics

```bash
bat config.yaml                 # highlighting + line numbers + pager
bat -n script.sh                # line numbers only, no header
bat -p log.txt                  # plain, no decorations
bat -r 40:80 main.go            # only lines 40–80
bat -A data.txt                 # show spaces, tabs, and newlines
```

## Combining with other commands

```bash
# read from a pipe and name the language
curl -s https://api.example.com/data | bat -l json

# colourise a list of changed files
git diff --name-only | xargs bat

# use it as your default pager
export PAGER="bat -p"
```

The most common pairing is as an `fzf` preview:

```bash
export FZF_DEFAULT_OPTS="--preview 'bat --color=always --style=numbers {}'"
```

## Configuration

```bash
bat --config-file        # print the config path
```

```bash
# ~/.config/bat/config
--theme="Monokai Extended"
--style="numbers,changes,header"
--italic-text=always
```

List available themes with `bat --list-themes`.

## Pretty only for humans

When its output goes to a pipe rather than a terminal, `bat` automatically behaves like
`cat`. So `bat file | grep x` in a script won't inject colour codes. To force colour,
add `--color=always`.

## Next

Make directory listings readable too → [eza](/docs/files/eza/)

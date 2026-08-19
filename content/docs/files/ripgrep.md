---
weight: 3010
title: "ripgrep"
description: "Much faster than grep, and it respects .gitignore without being asked."
icon: "manage_search"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`rg` is the tool with the most sensible defaults for searching a whole project. It
skips `node_modules` and `.git` on its own and uses every core you have.

## Install

```bash
brew install ripgrep          # macOS
sudo apt install ripgrep      # Ubuntu 18.10+
winget install BurntSushi.ripgrep.MSVC
```

## Basics

```bash
rg "createUser"                    # search the whole current directory
rg "createUser" src/               # limit to a directory
rg -i "createuser"                 # case insensitive
rg -w "id"                         # whole word (won't match "identity")
rg -F "a.b.c"                      # literal string, not a regex
```

## Options worth knowing

| Option | Meaning |
|---|---|
| `-t py` / `-t js` | Only files of a language (`rg --type-list` for the full set) |
| `-T test` | Exclude a type |
| `-g '*.md'` | Include by glob |
| `-g '!dist/*'` | Exclude by glob |
| `-l` | Print filenames only |
| `-c` | Count matches per file |
| `-n` | Line numbers (on by default) |
| `-A 3` / `-B 3` / `-C 3` | Context lines after / before / both |
| `--hidden` | Include hidden files |
| `-u` / `-uu` | Relax / ignore the ignore rules |

## In practice

```bash
# rank files by how many TODOs they carry
rg -c "TODO" | sort -t: -k2 -rn | head

# find env var usage with context
rg -C 2 "process\.env\." -t ts

# find declarations only
rg "^(export )?(async )?function \w+" -t ts

# search, then replace across the matches (needs sd)
rg -l "oldName" | xargs sd "oldName" "newName"
```

## Replacing text

ripgrep itself only previews replacements.

```bash
rg "oldName" -r "newName"     # shows the result; files are untouched
```

To change files, combine it with `sd` or `sed`.

```bash
brew install sd
rg -l "oldName" | xargs sd "oldName" "newName"
```

## Config file

Move repeated options out into a config.

```bash
# ~/.ripgreprc
--smart-case
--hidden
--glob=!.git/*
--max-columns=200
```

```bash
# ~/.zshrc
export RIPGREP_CONFIG_PATH="$HOME/.ripgreprc"
```

## Next

When you're searching by filename rather than content, reach for `fd` →
[fd](/docs/files/fd/)

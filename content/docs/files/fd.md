---
weight: 3020
title: "fd"
description: "Find files by name without remembering find's syntax."
icon: "folder_open"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

You don't have to remember
`find . -name "*.log" -type f -not -path "*/node_modules/*"`. In most cases `fd log`
covers it.

## Install

```bash
brew install fd
sudo apt install fd-find        # the binary is called fdfind
winget install sharkdp.fd
```

On Ubuntu, alias it:

```bash
alias fd=fdfind
```

## Basics

```bash
fd config                # anything with "config" in the name
fd -e md                 # files with the .md extension
fd -e md -e mdx docs/    # several extensions, in one directory
fd -H secret             # include hidden files
fd -t d node_modules     # directories only
```

## Options worth knowing

| Option | Meaning |
|---|---|
| `-e <ext>` | Filter by extension |
| `-t f` / `-t d` / `-t l` | Files / directories / symlinks |
| `-H` | Include hidden files |
| `-I` | Ignore `.gitignore` |
| `-d 2` | Limit search depth |
| `-s` | Case sensitive |
| `--changed-within 1d` | Modified in the last day |
| `-x <cmd>` | Run a command per result |
| `-X <cmd>` | Pass all results to one command |

## Acting on what you find

```bash
# convert every png to webp
fd -e png -x cwebp {} -o {.}.webp

# delete logs older than 30 days (list them first!)
fd -e log --changed-before 30d
fd -e log --changed-before 30d -X rm

# locate every package.json
fd -H '^package\.json$' -t f
```

`{}` is the path, `{.}` is the path without its extension, and `{/}` is the basename.

## Compared to find

| Goal | find | fd |
|---|---|---|
| By name | `find . -name "*conf*"` | `fd conf` |
| By extension | `find . -name "*.md"` | `fd -e md` |
| Directories only | `find . -type d -name dist` | `fd -t d dist` |
| Recently modified | `find . -mtime -1` | `fd --changed-within 1d` |

Because it honours `.gitignore` by default, results inside a project stay clean.
When you do need the build output, add `-I`.

## Next

Now that you can find files, make them pleasant to read →
[bat](/docs/files/bat/)

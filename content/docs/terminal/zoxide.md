---
weight: 2050
title: "zoxide"
description: "A cd replacement that remembers where you go and gets you there from a fragment of the name."
icon: "moving"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

If you type `cd ~/work/projects/backend/services/auth` several times a day, zoxide
shortens it to `z auth`. It scores directories you've visited by frequency and recency
and sends you to the most plausible one.

## Install

```bash
brew install zoxide
```

```bash
curl -sSfL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh
```

Hook it into your shell:

```bash
# ~/.zshrc
eval "$(zoxide init zsh)"

# bash
eval "$(zoxide init bash)"
```

To replace `cd` outright:

```bash
eval "$(zoxide init --cmd cd zsh)"
```

## Usage

```bash
# go about your day as usual; zoxide records it
cd ~/work/projects/backend

# afterwards, a fragment is enough
z backend
z proj back      # multiple fragments, space separated (AND)

# choose from the candidates (needs fzf)
zi backend
```

| Command | Action |
|---|---|
| `z <fragment>` | Jump to the highest-scoring match |
| `zi <fragment>` | Pick interactively from the candidates |
| `z -` | Back to the previous directory |
| `zoxide query -l` | Print recorded paths by score |
| `zoxide remove <path>` | Drop a path from the database |

## Things to know

- **The first few days do nothing.** The database has to fill up. Go about your work
  normally for a week and most jumps become two characters.
- **When names collide,** use `zi` and confirm rather than `z`. Never use `z` inside a
  script — use absolute paths there.
- To reset, delete `~/.local/share/zoxide/db.zo`.

## Good combinations

```bash
# jump, then search
z auth && rg "TODO"

# pick candidates with an fzf preview
zi
```

## Next

Before optimising anything, measure it →
[hyperfine](/docs/terminal/hyperfine/)

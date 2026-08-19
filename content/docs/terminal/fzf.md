---
weight: 2040
title: "fzf"
description: "An interactive filter for history, files, and branches. Five minutes to install, useful every day."
icon: "filter_alt"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

fzf takes a list and lets a human pick from it. Because it doesn't care what the list
is, it attaches to history, files, Git branches, processes, Docker containers —
anything.

## Install

```bash
brew install fzf
$(brew --prefix)/opt/fzf/install     # install the shell key bindings (answer y)
```

```bash
sudo apt install fzf
```

## Three keys you get for free

| Key | Action |
|---|---|
| `Ctrl+R` | Fuzzy-search command history (replaces the default reverse search) |
| `Ctrl+T` | Pick a file under the current directory and insert its path |
| `Alt+C` | Pick a subdirectory and `cd` into it |

`Ctrl+R` is the one you feel immediately. That long `docker run ...` from last month
comes back with two words.

## Pipe anything into it

```bash
# pick a file, open it in your editor
code "$(fzf)"

# pick a process and kill it
kill -9 $(ps aux | fzf | awk '{print $2}')
```

## Add a preview pane

With `bat` installed, you can show file contents alongside the list.

```bash
# ~/.zshrc
export FZF_DEFAULT_OPTS="--height 60% --layout=reverse --border --preview 'bat --color=always --style=numbers {}'"
export FZF_DEFAULT_COMMAND='fd --type f --hidden --exclude .git'
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
```

`fd` and `bat` are covered in [Files & Search](/docs/files/).

## Three functions worth stealing

Drop these in `~/.zshrc`:

```bash
# pick a branch and check it out
fbr() {
  local branch
  branch=$(git branch --all | grep -v HEAD | sed 's/.* //' | fzf) || return
  git checkout "${branch#remotes/origin/}"
}

# pick a commit and show it
fshow() {
  git log --oneline --color=always |
    fzf --ansi --preview 'git show --color=always {1}' |
    awk '{print $1}' | xargs git show
}

# shell into a running container
dsh() {
  local cid
  cid=$(docker ps --format '{{.ID}}\t{{.Names}}\t{{.Image}}' | fzf | cut -f1) || return
  docker exec -it "$cid" sh
}
```

## Search syntax

Symbols you can type in the fzf prompt:

| Input | Meaning |
|---|---|
| `abc` | Items containing a, b, c in order |
| `'abc` | Contains the exact string abc |
| `^abc` | Starts with abc |
| `abc$` | Ends with abc |
| `!abc` | Does not contain abc |
| `abc \| def` | Either one |

## Next

Commands are faster to find; now shorten directory travel →
[zoxide](/docs/terminal/zoxide/)

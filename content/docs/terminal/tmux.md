---
weight: 2030
title: "tmux"
description: "Sessions that survive a dropped SSH connection, split panes, and per-task windows — the minimum command set."
icon: "grid_view"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

tmux is a window manager that runs inside your terminal. Its two biggest wins are that
**work continues when your SSH connection drops** and that **a whole set of tasks can be
saved and brought back**.

## Install and first run

```bash
brew install tmux          # macOS
sudo apt install tmux      # Ubuntu/Debian

tmux new -s work           # start a session named "work"
```

To leave a session, press `Ctrl+B` then `d` (detach). Whatever was running on the
server keeps running. To come back:

```bash
tmux ls                    # list sessions
tmux attach -t work        # reattach
```

## Three concepts

| Concept | Meaning |
|---|---|
| **Session** | The unit of work. One project, one session |
| **Window** | A tab inside a session |
| **Pane** | A split within a window |

## Essential keys

Every shortcut starts with the prefix `Ctrl+B`, then the key.

| Key | Action |
|---|---|
| `c` | New window |
| `n` / `p` | Next / previous window |
| `0`–`9` | Jump to window by number |
| `%` | Split vertically |
| `"` | Split horizontally |
| `arrow keys` | Move between panes |
| `z` | Zoom the current pane / restore |
| `x` | Close a pane |
| `d` | Detach from the session |
| `[` | Enter scroll mode (`q` to leave) |

## Sanding down the defaults

`~/.tmux.conf`:

```bash
# Ctrl+A as the prefix; Ctrl+B is a stretch
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# resize and scroll with the mouse
set -g mouse on

# number windows from 1
set -g base-index 1

# splits open in the current directory
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"

# reload the config
bind r source-file ~/.tmux.conf \; display "Config reloaded"
```

## The pattern that matters

Running something long on a remote machine:

```bash
ssh server
tmux new -s migration
./migrate.sh            # a job that takes hours
# Ctrl+B, d — now close your laptop; it keeps running
```

Check on it the next day:

```bash
ssh server
tmux attach -t migration
```

## Next

With sessions handled, cut the time spent finding commands and files →
[fzf](/docs/terminal/fzf/)

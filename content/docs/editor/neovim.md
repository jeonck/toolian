---
weight: 4020
title: "Neovim"
description: "A modal editor driven entirely from the keyboard. Worth the investment if you work on servers often."
icon: "keyboard"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

The real advantage of Neovim isn't raw speed — it's that **it exists on every server
and your hands never leave the home row**. If you edit files over SSH regularly, the
basics alone pay for themselves.

## Install

```bash
brew install neovim
sudo apt install neovim
winget install Neovim.Neovim
```

## Survival commands

First, remove the possibility of being trapped in it.

| Command | Action |
|---|---|
| `Esc` | Back to normal mode (when in doubt, start here) |
| `i` / `a` | Insert before / after the cursor |
| `:w` | Save |
| `:q` | Quit |
| `:wq` or `ZZ` | Save and quit |
| `:q!` | Quit, discarding changes |

## Moving and editing

| Key | Action |
|---|---|
| `h j k l` | Left, down, up, right |
| `w` / `b` | Next / previous word |
| `0` / `$` | Start / end of line |
| `gg` / `G` | Start / end of file |
| `{n}G` | Go to line n |
| `dd` / `yy` / `p` | Delete / yank / paste a line |
| `u` / `Ctrl+r` | Undo / redo |
| `/text` then `n` | Search, then next match |
| `:%s/old/new/g` | Replace throughout the file |

Vim's grammar is **verb + object**. `d` (delete) + `w` (word) is `dw`; `c` (change) +
`i"` (inside quotes) is `ci"`. Once that composition clicks, there is far less to
memorise.

## Starting a config

`~/.config/nvim/init.lua`:

```lua
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.expandtab = true
vim.opt.shiftwidth = 2
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.clipboard = "unnamedplus"   -- share the system clipboard
vim.g.mapleader = " "               -- space as the leader key

vim.keymap.set("n", "<leader>w", ":w<CR>")
vim.keymap.set("n", "<leader>e", ":Explore<CR>")
```

## Starting from a distribution

If choosing plugins yourself sounds like a chore, start from a finished configuration.

| Distribution | Character |
|---|---|
| **LazyVim** | Balanced defaults, friendly documentation |
| **kickstart.nvim** | One heavily commented file. The best one to learn from |
| **AstroNvim** | Feature-rich with a polished UI |

```bash
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```

## A learning path

1. Run `vimtutor` in a terminal, start to finish (about 30 minutes).
2. For one week, edit only your config files in Neovim.
3. Once comfortable, turn on a Vim extension in VS Code and live in both worlds.

Switching your main editor in one go almost always fails. Start with small files.

## Next

If you refactor large projects often → [JetBrains IDEs](/docs/editor/jetbrains/)

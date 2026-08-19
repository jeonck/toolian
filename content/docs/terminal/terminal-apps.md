---
weight: 2010
title: "Choosing a Terminal"
description: "iTerm2, Windows Terminal, Ghostty, Warp — what differs, and what to change first."
icon: "web_asset"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

A terminal app is the window your shell lives in. A better window doesn't make commands
run faster, but easier tabs, splits, search, and copy keep you from losing your thread.

## The options

| App | Platform | Character |
|---|---|---|
| **iTerm2** | macOS | The long-standing default. Splits, search, session restore, triggers — the most features |
| **Ghostty** | macOS/Linux | GPU-accelerated and very fast. Configuration is a single text file |
| **Windows Terminal** | Windows | Tabs and splits, with WSL, PowerShell, and cmd in one window |
| **Warp** | macOS/Linux/Windows | Block-based output and AI command suggestions. Requires an account |
| **Built-in terminal** | Everywhere | Light and always present. Splits and search are the weak points |

Starting out, iTerm2 or Ghostty on macOS and Windows Terminal on Windows are safe picks.

## Four things to fix right after install

1. **Font.** Use a Nerd Font so prompt glyphs don't render as boxes.
   ```bash
   brew install --cask font-jetbrains-mono-nerd-font
   ```
   Then select `JetBrainsMono Nerd Font` in the app's font settings.
2. **Scrollback.** The default is often small enough to truncate a long log. Raise it
   to 10,000 lines or more.
3. **Key repeat rate.** On macOS, `System Settings → Keyboard` with repeat rate at
   maximum and delay at minimum makes cursor movement noticeably quicker.
4. **Learn the split keys.** iTerm2 uses `⌘D` (vertical) and `⌘⇧D` (horizontal);
   Windows Terminal uses `Alt+Shift++` and `Alt+Shift+-`.

## Shortcuts that work everywhere

| Action | macOS | Windows/Linux |
|---|---|---|
| Start / end of line | `Ctrl+A` / `Ctrl+E` | Same |
| Delete before / after cursor | `Ctrl+U` / `Ctrl+K` | Same |
| Delete a word | `Ctrl+W` | Same |
| Interrupt the running command | `Ctrl+C` | Same |
| Clear the screen | `Ctrl+L` | Same |
| Search history | `Ctrl+R` | Same |

Learn just these six and your arrow-key time disappears.

## Next

With the window settled, tidy up what's inside it →
[zsh and Starship](/docs/terminal/zsh-starship/)

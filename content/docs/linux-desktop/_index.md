---
weight: 95
title: "Linux Desktop"
description: "The desktop side of a Linux workstation — a browser that manages tabs for you, websites as real apps, and a keyboard-driven PDF reader."
icon: "desktop_windows"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Most of this site is about the terminal, because that's where the work happens. But you
spend the rest of the day in a browser with forty tabs, hunting for the window that has
the documentation in it, and squinting at a PDF in a viewer designed for a mouse.

Linux gives you unusual freedom to fix that — the desktop is assembled from parts, and
each part is replaceable.

| Page | Fixes |
|---|---|
| [Zen Browser](/docs/linux-desktop/zen-browser/) | Forty horizontal tabs you can no longer read |
| [Webapp Manager](/docs/linux-desktop/webapp-manager/) | The tools you use all day living inside a browser tab |
| [Zathura](/docs/linux-desktop/zathura/) | Reading a PDF with your hand on the mouse |

The thread running through all three is the same one behind
[tmux](/docs/terminal/tmux/) and [fzf](/docs/terminal/fzf/): **stop hunting, start
addressing.** A workspace you switch to, an app you Alt-Tab to, a page you jump to by
keystroke — each removes a small search you were performing dozens of times a day.

## Also worth a look

Three more that don't need a page of their own but belong in the same drawer:

- **Ulauncher** or **rofi** — a keystroke-driven launcher for apps, windows, and
  calculations. rofi if you live in a tiling window manager, Ulauncher if you want it to
  work out of the box.
- **Flameshot** — screenshots with annotation, bound to `PrtSc`. On Wayland check your
  compositor's support; **Spectacle** on KDE and **GNOME Screenshot** are the native
  fallbacks.
- **KDE Connect** — clipboard, files, and notifications shared between your phone and
  desktop, on any desktop environment, not just KDE.

Whatever you install, keep the configuration in
[dotfiles](/docs/getting-started/dotfiles/). The point of a desktop you tuned is being
able to reproduce it on the next machine in ten minutes.

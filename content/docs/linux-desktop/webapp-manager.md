---
weight: 9520
title: "Webapp Manager"
description: "Turning a website into a real desktop application — its own window, its own icon, its own place in Alt-Tab."
icon: "web_asset"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Half the tools you use all day are websites: the issue tracker, the chat, the calendar,
the dashboard. They live as tabs, which means they compete for attention with everything
else you opened, and Alt-Tab can't reach them — the browser is one window, and the thing
you want is somewhere inside it.

[Webapp Manager](https://github.com/linuxmint/webapp-manager) fixes that in about ten
seconds per site. It creates a launcher that opens the URL in its own chrome-less window,
with its own icon, its own entry in the application menu, and its own slot in the window
switcher. From the desktop's point of view, it is an application.

Linux Mint's team wrote it, and it works on any distribution.

## Install

```bash
sudo apt install webapp-manager                       # Mint
sudo dnf copr enable refi64/webapp-manager && sudo dnf install webapp-manager   # Fedora
yay -S webapp-manager                                 # Arch, from the AUR
```

On Debian and Ubuntu the `.deb` from Mint's repository installs cleanly.

One gotcha that costs people an hour: **a Snap-confined browser won't be detected.** If
Webapp Manager shows no browsers on Ubuntu, install a non-Snap Firefox, Chromium, or
Brave and it will find it.

## Create one

Open it, press **+**, and fill in four fields:

| Field | Note |
|---|---|
| Name | What appears in the menu and the switcher |
| Address | The deepest URL that's useful — your board, not the site's front page |
| Category | Where it lands in the application menu |
| Browser | Firefox, Chromium, or whichever you have; each gets its own profile |

Two options worth setting: **Isolated profile** gives the app its own cookies and session,
which is how you stay signed into two accounts of the same service at once — one work
Gmail app, one personal. And **navigation bar** off is the default for a reason; turn it
on only for sites that need you to type a URL.

The result is a `.desktop` file in `~/.local/share/applications/`, so it's ordinary,
inspectable, and easy to back up with your [dotfiles](/docs/getting-started/dotfiles/).

## What it's good for

- **The tool you keep losing.** Chat, calendar, and the ticket board stop being tabs and
  start being windows you can pin to a workspace.
- **Two accounts, no signing out.** Isolated profiles, one app per account.
- **Focus.** An app window has no other tabs in it. That's most of the benefit, and it's
  hard to appreciate until you try it for a week.
- **Web apps that behave like software** — Excalidraw, Figma, a Grafana dashboard on a
  second screen.

If your browser has a "install this site as an app" feature, that's the same idea with
fewer options; Webapp Manager gives you the icon, the category, the profile isolation,
and a list you can manage in one place.

## Where it doesn't help

It's still a browser window: the same memory, the same extensions story, and no offline
capability the site doesn't already have. And a site that opens links in new tabs will
still bounce you into your main browser — usually what you want, occasionally jarring.

## Next

The last thing on the desktop worth replacing → [Zathura](/docs/linux-desktop/zathura/)

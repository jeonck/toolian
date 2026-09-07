---
weight: 13020
title: "Puter"
description: "A desktop — files, notepad, spreadsheet, app store — running in a browser tab, on a server you control."
icon: "desktop_windows"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[Puter](https://github.com/HeyPuter/puter) is the odd one in this guide, and worth twenty
minutes purely as a thing to look at. It's a **desktop environment that runs in a browser
tab**: a window manager, a file system, a notepad, a spreadsheet, a camera, a terminal, an
app store — all of it web, all of it self-hostable, AGPL-3.0.

## Try it

The hosted version at [puter.com](https://puter.com) needs nothing. To run your own:

```bash
curl -fsSL https://puter.com/selfhost | sh          # Linux and macOS
```

```powershell
irm https://puter.com/selfhost?os=windows | iex     # Windows
```

Or from source, which is the honest way to see how it works:

```bash
git clone https://github.com/HeyPuter/puter
cd puter && npm install && npm start
# → http://puter.localhost:4100
```

Piping an installer straight into a shell is a habit worth resisting in general — read
the script first, as you would for anything else.

## Two things it's for

**As a user**, it's a cloud drive with the small applications around it: store files,
open one in a text editor or spreadsheet without downloading it, share a link. On your own
server, that's a personal drive whose storage limit is your disk.

**As a developer**, it's a platform. Puter exposes cloud storage, a key-value database,
AI calls, and serverless workers to apps that run inside it, plus an app store to publish
to. Building a small tool against those APIs is genuinely fast, because the account
system, storage, and hosting are already there.

## Being realistic about it

- **It's a computing environment, not a replacement for your operating system.** The
  browser sandbox is the ceiling: no native applications, no local device access beyond
  what the web platform grants.
- **The apps are capable, not Office.** A notepad and a spreadsheet that work well enough
  in a tab — reach for [real ones](/docs/writing/) when the document matters.
- **A self-hosted instance is a web application holding your files.** Authentication,
  TLS, and backups are yours. Behind [Tailscale](/docs/network/tailscale/) is the easy
  answer for personal use.
- **The hosted version is someone else's server again** — fine for trying it, worth
  remembering when the point of the exercise was to stop doing that.

## That's the tour

You've been through every category. To start again, pick another from the
[overview](/docs/).

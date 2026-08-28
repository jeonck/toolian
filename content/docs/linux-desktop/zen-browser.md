---
weight: 9510
title: "Zen Browser"
description: "A Firefox fork built around vertical tabs, workspaces, and split view — for the days that end with forty tabs open."
icon: "tab"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Horizontal tabs stop working at about fifteen. The favicons become the label, the label
becomes a guess, and finding the tab with the API documentation takes longer than
searching for it again.

[Zen Browser](https://zen-browser.app/) is a Firefox fork that rebuilds the interface
around that problem: **vertical tabs** down the side where titles are readable,
**workspaces** that separate one context from another, and a **split view** that tiles up
to four pages at once. It's open source, it renders with Gecko, and your Firefox
extensions and profile come with you.

## Install

```bash
flatpak install flathub app.zen_browser.zen        # the usual route on any distro
yay -S zen-browser-bin                             # Arch, from the AUR
```

Tarballs for other distributions are on the site, and it's on macOS and Windows too.
First launch offers to import bookmarks, passwords, and history from another browser.

## The three features that change the day

**Workspaces.** One for work, one for a side project, one for whatever you're reading.
Each holds its own tabs, so switching context hides everything belonging to the other one
instead of leaving it in the strip. This is the feature people stay for — closing a
workspace is closing a chapter, not losing forty tabs.

**Vertical tabs with pinned essentials.** Pin the four things you always have open; the
rest scroll below them with readable titles. Compact mode collapses the sidebar to icons
until you reach for it, which gives a laptop screen its vertical space back.

**Split view.** Documentation on the left, the thing you're building on the right — up to
four panes in a grid. It removes a surprising amount of Alt-Tabbing when you're following
a guide.

Then two smaller ones worth knowing: **Glance** opens a link in an overlay so a quick
check doesn't become another tab, and the interface is themeable to an unusual degree if
you like that sort of thing.

## Be clear-eyed about the trade

- **It's a fork, so security patches arrive after Mozilla ships them.** The lag is usually
  short and the project tracks Firefox releases closely, but "usually short" is the
  correct expectation, not "immediately". If your threat model doesn't tolerate that, run
  Firefox with its own vertical tabs — it has them now — or Floorp.
- **Version numbers still carry a `b`.** It's stable enough for daily use and it is not
  claiming to be finished.
- **It's Gecko, not Blink.** Rendering is Firefox's, so a site built and tested only
  against Chrome may misbehave. Benchmarks put it behind Chrome and Safari; in practice
  the difference is smaller than the tab-hunting it saves.
- **A fork is one team's roadmap.** Keep your bookmarks and passwords synced somewhere you
  control, so moving back is an afternoon and not a project.

## Next

Some sites don't want to be tabs at all → [Webapp Manager](/docs/linux-desktop/webapp-manager/)

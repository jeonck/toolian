---
weight: 9010
title: "Raycast (macOS)"
description: "A Spotlight replacement covering app launching, clipboard history, window management, and snippets."
icon: "rocket_launch"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Raycast opens with `⌘Space` and runs whatever you want after a few keystrokes. It does
more than launch apps — it **removes most of the reasons your hand reaches for the
mouse**.

## Install

```bash
brew install --cask raycast
```

After installing, unbind `⌘Space` from Spotlight (System Settings → Keyboard →
Keyboard Shortcuts → Spotlight) and give it to Raycast.

## Four things to turn on immediately

| Feature | Why |
|---|---|
| **Clipboard History** | Go back and retrieve something you copied. The biggest single win |
| **Window Management** | Halve and quarter windows without a separate app |
| **Snippets** | Expand a short keyword into a phrase or address you type constantly |
| **Quicklinks** | Frequently opened URLs, with parameters |

Give Clipboard History its own shortcut, something like `⌥⌘C`, and it's ready to use.

## Snippet examples

| Keyword | Expands to |
|---|---|
| `;email` | Your email address |
| `;date` | `{date}` inserts today's date |
| `;sig` | Your mail signature |
| `;pr` | A pull request description template |

## Quicklink examples

Put `{query}` in the URL and your input gets substituted.

```
https://github.com/search?q={query}&type=code
https://translate.google.com/?text={query}
jira://browse/{query}
```

## Extensions

Install from the Raycast Store. The ones that earn their place:

- **GitHub** — search PRs and issues, check status
- **Homebrew** — search and install packages
- **Kill Process** — free up a port something is holding
- **Color Picker** — sample a colour from anywhere on screen
- **Search Emoji**

## Script commands

Your own shell scripts can become Raycast commands.

```bash
#!/bin/bash
# @raycast.schemaVersion 1
# @raycast.title Restart dev server
# @raycast.mode compact
# @raycast.packageName Project

cd ~/projects/my-app && docker compose restart app
```

Make the file executable (`chmod +x`) and register its folder under Script Commands in
Raycast settings; it then appears in the list.

## Elsewhere

| OS | Tool |
|---|---|
| Windows | PowerToys Run, Flow Launcher |
| Linux | Ulauncher, Albert, rofi |

## Next

On Windows → [PowerToys](/docs/automation/powertoys/)

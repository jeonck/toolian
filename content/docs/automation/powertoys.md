---
weight: 9020
title: "PowerToys (Windows)"
description: "Microsoft's free utility bundle — window layouts, a launcher, key remapping, and screen OCR."
icon: "widgets"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

PowerToys is an open-source collection of small utilities in one app. Turn on only the
modules you need.

## Install

```powershell
winget install Microsoft.PowerToys
```

## Modules worth enabling

| Module | What it does |
|---|---|
| **FancyZones** | Split the screen into a grid and snap windows into it |
| **PowerToys Run** | An `Alt+Space` launcher for apps, files, calculations, commands |
| **Keyboard Manager** | Remap keys and shortcuts |
| **Text Extractor** | OCR text from anywhere on screen (`Win+Shift+T`) |
| **PowerRename** | Bulk rename with regular expressions |
| **Always On Top** | Pin a window (`Win+Ctrl+T`) |
| **Color Picker** | Sample a colour (`Win+Shift+C`) |
| **Awake** | Temporarily prevent sleep |

## FancyZones

Open the editor with `Win+Shift+\``, build a layout, then hold `Shift` while dragging a
window to snap it into a zone. Fixing three windows at a 3:5:4 ratio on a wide monitor
removes dragging-to-resize from your day entirely.

Enable "move windows between zones with the arrow keys" in settings and `Win+arrow`
works too.

## Keyboard Manager

The most common use is repurposing `CapsLock`.

| From | To | Why |
|---|---|---|
| CapsLock | Ctrl | Less wrist strain; useful for Vim users |
| CapsLock | Esc | For Vim/Neovim |
| Insert | (nothing) | Stops accidental overtype mode |

It also supports per-application remapping, so a key can behave differently in one
program.

## PowerRename

Select several files, right-click, PowerRename.

| Search | Replace | Result |
|---|---|---|
| `IMG_(\d+)` | `photo_$1` | `IMG_0231.jpg` → `photo_0231.jpg` |
| ` ` | `_` | Spaces to underscores |
| `(.*)\.jpeg` | `$1.jpg` | Normalise the extension |

Capture groups (`$1`) need the regular expression checkbox on. Check the preview before
applying.

## Text Extractor

No more retyping code or an error message out of an image. `Win+Shift+T`, drag a region,
and the text lands on your clipboard. Especially good for logs that arrived as a
screenshot.

## Working with WSL

WSL is the easiest way to get Linux CLI tools on Windows.

```powershell
wsl --install -d Ubuntu
```

Afterwards, open an Ubuntu tab in Windows Terminal and every Linux install command on
this site works as written. Files are reachable from Explorer at
`\\wsl$\Ubuntu\home\<user>`.

## Next

To collect a project's commands in one place → [Make](/docs/automation/make/)

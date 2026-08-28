---
weight: 10020
title: "Joplin"
description: "Open-source notes with end-to-end encrypted sync, a notebook hierarchy, and an external editor of your choosing."
icon: "note_alt"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Obsidian](/docs/writing/obsidian/) keeps your notes as plain files and is free but not
open source; Evernote holds them in a service you can't inspect.
[Joplin](https://joplinapp.org/) takes the third position: **open source end to end**,
with notes stored locally and synced encrypted to storage you choose.

If the thing that matters is that nobody else can read your notes and nobody can change
the terms on you, this is the one.

## Install

```bash
wget -O - https://raw.githubusercontent.com/laurent22/joplin/dev/Joplin_install_and_update.sh | bash
```

That script is the **official** Linux route — it installs the AppImage and wires up the
desktop entry. The Flatpak is community-maintained and not supported by the project, which
is worth knowing before you file a bug against it. There's a terminal client too:

```bash
npm install -g joplin
joplin                 # a full TUI, if you'd rather not leave the terminal
```

Desktop, mobile, and a browser clipper for saving pages complete the set.

## Sync, and the encryption

Joplin doesn't run the storage — you point it at one:

| Target | Notes |
|---|---|
| **Joplin Cloud** | Paid, run by the project, supports sharing and collaboration |
| **Dropbox / OneDrive** | Free tiers, easy setup |
| **WebDAV / Nextcloud** | Self-hosted, your server |
| **S3-compatible** | Including [R2](/docs/vibe-infra/cloudflare-r2/) |
| **File system** | A synced folder, or nothing at all |

Then turn on **end-to-end encryption** in the settings before the first sync. Notes are
encrypted on your device, so the storage provider holds ciphertext. Save the master
password somewhere you will still have it after a laptop dies — there is no recovery, by
design.

## Organising, and writing in your own editor

The model is notebooks (nestable), notes, and tags, with search across everything. That
hierarchy is the main structural difference from Obsidian's flat-folder-plus-links
approach: if you think in folders, Joplin will feel right immediately.

The feature that makes it comfortable for people who already have an editor:

```
Tools → Options → General → Text editor command:  gvim -f
                                                  code --wait
                                                  kitty -e nvim
```

Press `Ctrl+E` on a note and it opens in that editor. Save and close, and Joplin picks up
the change. You get Vim bindings, your snippets, and your spell-checker on a note that's
still synced and encrypted — which is exactly the arrangement people usually have to give
up when they leave plain files.

Notes are Markdown, and the underlying store is SQLite plus attachments, so nothing is
locked away: export to Markdown, JEX, HTML, or PDF whenever you like.

## Joplin or Obsidian?

| | Joplin | [Obsidian](/docs/writing/obsidian/) |
|---|---|---|
| Licence | Open source | Free, proprietary |
| Storage | SQLite + attachments, export to Markdown | Plain Markdown files on disk |
| Sync | Any backend, E2EE included | Paid official sync, or your own Git/Syncthing |
| Structure | Notebooks and tags | Folders, links, and a graph |
| Plugins | A smaller ecosystem | Very large |

Rough rule: **Obsidian** if you want your notes to be a folder of Markdown you can grep
and link heavily; **Joplin** if encrypted sync you control and an open-source stack matter
more than the plugin ecosystem.

## Next

Whatever tool you use, the syntax is the same → [Markdown](/docs/writing/markdown/)

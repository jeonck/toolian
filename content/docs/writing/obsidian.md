---
weight: 10010
title: "Obsidian"
description: "Personal knowledge management on top of local Markdown files, linked together."
icon: "menu_book"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

The heart of Obsidian is that **a note is just a `.md` file in a folder**. If the
service disappears the files remain, and version-controlling them or moving them to
another tool is easy.

## Install and first setup

```bash
brew install --cask obsidian
```

- A **vault** is the folder holding your notes. Make one at something like `~/notes`.
- Under Settings → Files & Links, point "Default location for new attachments" at an
  `attachments` folder so images don't scatter.

## Folder structure

A deep folder tree makes you deliberate about where things go, and then you stop writing
them down. Staying shallow and linking instead lasts longer.

```
notes/
  inbox/        # dump things here first
  daily/        # by date
  projects/     # work in flight
  reference/    # settled knowledge
  attachments/
```

## Linking

```markdown
See [[Docker networking]].
[[Docker networking|container communication]] changes the display text.
```

Linking to a note that doesn't exist is fine — clicking it later creates it under that
name. This "link first" habit is the most natural way for a set of notes to grow.

The **Backlinks** panel at the bottom of a note shows what refers to it. That's the
connection folder hierarchies can never give you.

## Daily notes

Enable Settings → Core plugins → Daily notes and point it at a template.

```markdown
## Done today

## Stuck on

## Learned

## Tomorrow
```

Recording in the same shape every day means you can later search your way back to "that
error from before."

## Core plugins worth using

| Plugin | Purpose |
|---|---|
| Daily notes | Automatic dated notes |
| Templates | Insert note templates |
| Quick switcher | `⌘O` to search note names |
| Command palette | `⌘P` for everything |
| Outgoing links | What this note refers to |
| Graph view | Visualise connections (fun, less practical) |

## Community plugins

| Plugin | Purpose |
|---|---|
| **Dataview** | Generate tables and lists from note metadata |
| **Templater** | Dynamic templates with dates and variables |
| **Excalidraw** | Hand-drawn diagrams inside a note |
| **Obsidian Git** | Auto-commit and push the vault |

A Dataview example that keeps a project list current:

````markdown
```dataview
TABLE status, due
FROM "projects"
WHERE status != "done"
SORT due ASC
```
````

## Syncing

- **Obsidian Sync**: paid, end-to-end encrypted, and the simplest to set up.
- **Git**: free, with the Obsidian Git plugin auto-committing. You may have to resolve
  conflicts, so it suits text-heavy vaults.
- **iCloud/Dropbox**: free, but prone to conflict copies when editing on two devices.

If you use Git, put these in `.gitignore`:

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```

## Alternatives

| Tool | Character |
|---|---|
| **Notion** | Strong collaboration and databases; files live in the cloud |
| **Logseq** | Block-based outliner over local files |
| **Apple Notes** | Light and fast; limited linking |

Obsidian for the knowledge you accumulate alone and Notion for shared team documents is
a common split.

## Next

For the same job with open-source sync you control → [Joplin](/docs/writing/joplin/)

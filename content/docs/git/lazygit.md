---
weight: 6040
title: "lazygit"
description: "A Git UI inside the terminal. Staging, rebasing, and conflict resolution from the keyboard."
icon: "dashboard"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

lazygit combines `git status`, `git add -p`, `git log`, and `git rebase -i` into one
screen. It speeds up **partial staging and interactive rebase** in particular.

## Install and run

```bash
brew install lazygit
sudo apt install lazygit
winget install JesseDuffield.lazygit

cd ~/projects/my-app
lazygit                     # or alias lg='lazygit'
```

## Layout

Five panels run down the left; move between them with `1`–`5` or `Tab`.

| Number | Panel | Purpose |
|---|---|---|
| 1 | Status | Repository state and settings |
| 2 | Files | Changed files, staging |
| 3 | Branches | Switching and merging |
| 4 | Commits | History and rebasing |
| 5 | Stash | Temporary storage |

## Essential keys

| Key | Action |
|---|---|
| `Space` | Toggle staging for a file or hunk |
| `Enter` | Enter a file for line-level staging |
| `c` | Commit |
| `A` | Amend the previous commit |
| `p` | Pull |
| `P` | Push |
| `d` | Delete / discard |
| `?` | Help for the current panel |
| `q` | Quit |

You don't have to memorise anything — `?` lists the keys in place.

## Line-level staging

More direct than `git add -p`:

1. Select a file in the Files panel and press `Enter`
2. Move between hunks with the arrow keys; `Space` stages a hunk
3. Press `v` to select a range, then `Space` to stage exactly those lines
4. `Esc` to come back out, `c` to commit

Perfect for committing only the real change out of a file that also has debug code in it.

## Interactive rebase

In the Commits panel (`4`):

| Key | Action |
|---|---|
| `s` | Squash into the commit below |
| `f` | Fixup (squash, discard the message) |
| `r` | Reword |
| `d` | Drop the commit |
| `Ctrl+J` / `Ctrl+K` | Move a commit up or down |

The rebase applies automatically when you're done. On a conflict, fix it in the Files
panel and choose continue from the `m` menu.

## Configuration

Find the path with `lazygit --print-config-dir`, then create `config.yml`.

```yaml
gui:
  showFileTree: true
  mouseEvents: true
  theme:
    selectedLineBgColor: ["reverse"]
git:
  paging:
    colorArg: always
    pager: delta --dark --paging=never
```

Setting `delta` as the pager makes diffs far easier to read — which is the next page.

## Next

Make the diff itself readable → [delta](/docs/git/delta/)

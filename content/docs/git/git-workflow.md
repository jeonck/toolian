---
weight: 6010
title: "The Daily Git Workflow"
description: "Branching, committing, and rebasing in practice — plus how to undo your mistakes."
icon: "account_tree"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Git has hundreds of commands; you use about ten of them daily. Here are those ten, plus
how to get out of trouble.

## A day's shape

```bash
git switch main && git pull --rebase     # get current
git switch -c feat/login-rate-limit      # branch off
# ... work ...
git add -p                               # stage hunk by hunk, reviewing as you go
git commit -m "Limit login attempts to five per minute"
git push -u origin feat/login-rate-limit
```

`git add -p` walks you through each hunk and asks whether to include it. It prevents
most stray-`print`-statement accidents on its own.

## Commands you'll use

| Goal | Command |
|---|---|
| Terse status | `git status -sb` |
| See changes | `git diff`, or `git diff --staged` for staged ones |
| One-line log | `git log --oneline --graph --decorate -20` |
| Switch branch | `git switch <branch>` |
| New branch | `git switch -c <branch>` |
| Set work aside | `git stash`, restore with `git stash pop` |
| Take one commit | `git cherry-pick <hash>` |
| Prune remote branches | `git fetch --prune` |

## Commit messages

Settling on a format makes history far easier to read later. A widely used convention:

```
feat: limit login attempts to five per minute
fix: return 401 instead of 500 for expired tokens
refactor: move auth middleware into auth/
docs: add a rollback step to the deploy runbook
test: cover token refresh failure
chore: update dependencies
```

Keep the subject under 50 characters and in the imperative. If you need a body, leave a
blank line and explain **why**. The diff already says what.

## Undoing things

| Situation | Command |
|---|---|
| Fix the last commit message | `git commit --amend` |
| Undo the last commit, keep changes | `git reset --soft HEAD~1` |
| Unstage only | `git restore --staged <file>` |
| Throw away file changes | `git restore <file>` |
| Revert an already-pushed commit | `git revert <hash>` |
| Rewind a branch wholesale | `git reset --hard <hash>` (careful) |
| Find a lost commit | `git reflog`, then `git reset --hard <hash>` |

`git reflog` is the last safety net. Even commits destroyed by `reset --hard` are
usually still there (90 days by default).

The rule is **`revert` for pushed commits, `reset` for local ones only**. Rewriting
shared history breaks everyone else's clone.

## Rebase and merge

```bash
# replay your branch on top of the latest main (linear history)
git switch feat/login
git fetch origin
git rebase origin/main

# on a conflict, fix the files then
git add <file>
git rebase --continue

# to give up
git rebase --abort
```

To tidy commits before pushing, use an interactive rebase.

```bash
git rebase -i origin/main
# change pick to squash (s) to fold a commit into the one above it
```

## Settings worth having

```bash
git config --global pull.rebase true          # no merge commits on pull
git config --global push.autoSetupRemote true # -u becomes unnecessary
git config --global init.defaultBranch main
git config --global rerere.enabled true       # remember conflict resolutions
git config --global fetch.prune true
```

## Next

To handle PRs and issues without a browser → [GitHub CLI](/docs/git/github-cli/)

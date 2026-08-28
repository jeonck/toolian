---
weight: 6020
title: "GitHub CLI (gh)"
description: "Creating, reviewing, and merging PRs, managing issues, and watching Actions from the terminal."
icon: "terminal"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`gh` moves most of what you do on github.com into the terminal. The biggest gain is
losing the open-a-browser, find-the-tab, click-the-button loop.

## Install and authenticate

```bash
brew install gh
sudo apt install gh
winget install GitHub.cli

gh auth login          # browser flow
gh auth status         # check who you are
```

## Pull requests

```bash
gh pr create --fill                  # title and body from your commits
gh pr create --draft --title "WIP: auth refactor"

gh pr list                           # open PRs
gh pr list --author "@me"            # just yours
gh pr status                         # everything relevant to you

gh pr checkout 42                    # switch to a PR branch to review it
gh pr diff 42                        # see the changes
gh pr review 42 --approve
gh pr review 42 --request-changes -b "Could you add a test for this?"
gh pr merge 42 --squash --delete-branch
```

`gh pr checkout` is especially good when reviewing: you don't need to know the remote
branch name, just the number.

## Issues

```bash
gh issue create --title "Redirect fails after login" --body "Steps to reproduce..."
gh issue list --label bug --state open
gh issue view 17 --comments
gh issue close 17
```

## Watching Actions

```bash
gh run list --limit 5          # recent runs
gh run watch                   # follow the run in progress
gh run view <run-id> --log-failed   # only the failing step's log
gh workflow run deploy.yml     # trigger manually
```

`gh run watch` replaces the push-then-refresh-the-browser habit entirely.

## Repositories

```bash
gh repo create myorg/newrepo --private --source=. --remote=origin --push
gh repo clone jeonck/toolian
gh repo view --web            # open it in a browser
gh release create v1.2.0 --generate-notes
```

## Calling the API directly

Anything the CLI doesn't wrap, call with `gh api` — authentication comes along for free.

```bash
gh api repos/jeonck/toolian --jq '.stargazers_count'
gh api -X PUT repos/jeonck/toolian/pages -f cname=toolian.metacog.co.kr
```

For boolean values, use capital `-F` rather than lowercase `-f`, so a real JSON boolean
goes over the wire instead of a string.

```bash
gh api -X PUT repos/OWNER/REPO/pages -F https_enforced=true
```

## Aliases

```bash
gh alias set prs 'pr list --author "@me"'
gh alias set co 'pr checkout'
gh prs
gh co 42
```

## Next

When the code can't live on GitHub at all → [Gitea](/docs/git/gitea/)

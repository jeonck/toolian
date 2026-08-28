---
weight: 6080
title: "pre-commit Hooks"
description: "Running formatters, linters, and secret scanning at commit time so review never has to mention them."
icon: "verified"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

"Formatting is off here" and "there's a leftover console log" are review comments a
machine should be making, not a person. pre-commit runs a fixed set of checks every
time you commit.

## Install

```bash
brew install pre-commit
pip install pre-commit
```

Install the hooks from the project root:

```bash
pre-commit install
```

They now run on every `git commit`.

## The config file

`.pre-commit-config.yaml` at the project root:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-merge-conflict
      - id: check-added-large-files
        args: ["--maxkb=1000"]

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.19.2
    hooks:
      - id: gitleaks
```

`gitleaks` stops API keys and tokens from slipping into a commit. Since a pushed secret
is effectively unrecoverable, that hook alone justifies adopting this.

## Commands

| Command | Action |
|---|---|
| `pre-commit run` | Run against staged files |
| `pre-commit run --all-files` | Run against the whole repo (once, at adoption) |
| `pre-commit autoupdate` | Bump hook versions |
| `pre-commit run <hook-id>` | Run a single hook |
| `git commit --no-verify` | Skip the hooks (emergencies only) |

## Rolling it out

1. Run `--all-files` first and put the result in **its own commit**. Mixing formatting
   with behaviour changes makes review impossible.
2. Add that commit's hash to `.git-blame-ignore-revs` so blame stays useful.
   ```bash
   echo "<formatting-commit-hash>" >> .git-blame-ignore-revs
   git config blame.ignoreRevsFile .git-blame-ignore-revs
   ```
3. Tell the team to run `pre-commit install` once. Hooks are not applied automatically
   by cloning; everyone installs them.
4. Run the same checks in CI so commits from people who skipped step 3 still get caught.

```yaml
# part of .github/workflows/lint.yml
- uses: pre-commit/action@v3.0.1
```

## When it gets slow

- Move heavy hooks (type checking, tests) to pre-push or CI.
  ```bash
  pre-commit install --hook-type pre-push
  ```
- Narrow the scope with `files:` and `exclude:`.

## Next

With the code tidy, on to talking to the outside world →
[Network & APIs](/docs/network/)

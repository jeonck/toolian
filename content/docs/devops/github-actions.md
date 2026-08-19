---
weight: 8040
title: "GitHub Actions"
description: "Running tests, builds, and deploys automatically on every push."
icon: "play_circle"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Actions is CI/CD that runs whenever you put a workflow YAML file in your repository.
There's no server to stand up, so it's painless even for personal projects.

## A first workflow

`.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - run: npm ci
      - run: npm run lint
      - run: npm test
```

Push and it runs. Follow along from the terminal with `gh run watch`.

## Core concepts

| Term | Meaning |
|---|---|
| **workflow** | One YAML file defining triggers and jobs |
| **job** | Runs on its own runner; jobs are parallel by default |
| **step** | A sequential unit inside a job |
| **action** | A reusable step (`uses:`) |
| **runner** | The execution environment (`ubuntu-latest`, etc.) |

## Common patterns

**Testing several versions (matrix)**

```yaml
strategy:
  matrix:
    node: [20, 22]
    os: [ubuntu-latest, macos-latest]
runs-on: ${{ matrix.os }}
```

**Caching dependencies**

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: pip-${{ hashFiles('requirements.txt') }}
```

`setup-node`, `setup-python`, and friends handle this more simply via `cache:`.

**Conditional steps**

```yaml
- name: Deploy
  if: github.ref == 'refs/heads/main' && github.event_name == 'push'
  run: ./deploy.sh
```

**Job dependencies**

```yaml
jobs:
  build: { ... }
  deploy:
    needs: build
    runs-on: ubuntu-latest
```

## Secrets

Register them under `Settings → Secrets and variables → Actions`, then use them:

```yaml
- run: ./deploy.sh
  env:
    API_TOKEN: ${{ secrets.API_TOKEN }}
```

Secrets are masked in logs automatically, but a script that transforms and prints the
value can still leak it. Don't `echo` them.

## Least privilege

Declaring token permissions per workflow is the safer default.

```yaml
permissions:
  contents: read
  pull-requests: write
```

## Debugging

| Problem | Check |
|---|---|
| The workflow never runs | File location (`.github/workflows/`), branch, YAML syntax |
| Passes locally, fails in CI | Missing tooling, environment variables, filename casing |
| Cache never hits | Whether the key includes a lockfile hash |
| Permission errors | The `permissions:` block and token scopes |

```bash
gh run list --limit 5
gh run view <id> --log-failed
```

To run a workflow locally, use [act](https://github.com/nektos/act).

```bash
brew install act
act -j test
```

## Watch the cost

Public repositories are free, but private ones are billed by minute. macOS runners cost
around ten times a Linux one, so use them only when you must. Cancelling superseded runs
with `concurrency` cuts waste.

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## Next

To manage the infrastructure itself as code → [Terraform](/docs/devops/terraform/)

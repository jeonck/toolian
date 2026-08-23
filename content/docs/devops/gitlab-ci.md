---
weight: 8060
title: "GitLab CI/CD"
description: "Repository, pipelines, registry, and releases in one system you can run inside your own network."
icon: "integration_instructions"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

GitLab's pitch is that everything lives in one place: source, merge requests, pipelines,
container registry, packages, issues, releases. On the public SaaS that's a convenience.
On an **on-premise install inside a closed network**, it's often the only way to have
GitHub-shaped workflows at all — which is why so many enterprise and public-sector teams
land here.

The CI is not a bolt-on either. It's the same YAML-in-the-repo model as
[GitHub Actions](/docs/devops/github-actions/), with a runner architecture designed to be
self-hosted from the start.

## A first pipeline

`.gitlab-ci.yml` at the repository root:

```yaml
stages: [test, build, deploy]

default:
  image: node:22-alpine
  cache:
    key:
      files: [package-lock.json]
    paths: [.npm/]

test:
  stage: test
  script:
    - npm ci --cache .npm --prefer-offline
    - npm run lint
    - npm test
  artifacts:
    reports:
      junit: reports/junit.xml

build:
  stage: build
  script: npm run build
  artifacts:
    paths: [dist/]
    expire_in: 1 week

deploy:
  stage: deploy
  script: ./deploy.sh
  environment:
    name: production
    url: https://example.com
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

Push and it runs. Jobs in the same stage run in parallel; stages run in order — or skip
the ordering entirely with `needs:` to build a DAG where each job starts the moment its
own inputs are ready.

## Things worth knowing early

| Feature | Why it matters |
|---|---|
| `rules:` | Decides whether a job runs at all. Replaces the older `only/except` |
| `artifacts:` | Files passed to later stages, and shown in the merge request |
| `environment:` | Tracks what is deployed where, with a one-click rollback |
| `include:` | Pull shared pipeline templates from another repository |
| `parallel: matrix:` | The version and platform matrix, same idea as elsewhere |
| Review apps | An ephemeral deployment per merge request |

Variables live in **Settings → CI/CD → Variables**. Mark secrets **masked** so they're
hidden in logs, and **protected** so they're only exposed on protected branches — the
second flag is the one people forget, and it's the one that keeps a fork's pipeline away
from production credentials.

## Runners are the real decision

A runner is the machine that executes jobs, and where it lives shapes both the bill and
the security story.

- **GitLab.com shared runners** — 400 compute minutes a month on the free tier. Fine for
  a small project, exhausted quickly by a real one.
- **Your own runners** — free and unlimited on every tier, including free. Install
  `gitlab-runner`, register it against the project or group, pick an executor (Docker is
  the usual choice), and the minute pool stops mattering.
- **Self-managed GitLab** — the Community Edition is free software; you pay in
  infrastructure and in operations.

```bash
gitlab-runner register \
  --url https://gitlab.example.com/ \
  --token <runner-token> \
  --executor docker \
  --docker-image alpine:latest
```

Tag runners (`gpu`, `windows`, `prod-network`) and select them per job with `tags:`.
Untagged jobs land on whatever is free, which is how a build ends up on the one machine
with production access.

## Before you choose self-managed

Running GitLab yourself means you own upgrades, backups, and a runner fleet — a real,
recurring job, not a weekend install. Take it on when the requirement is genuine: source
that cannot leave the network, an auditor who wants one system of record, or an
environment with no internet route at all. If none of those apply, GitLab.com or GitHub
will cost you less attention.

## Next

Building the artifact is half of it. Getting it onto the cluster, and keeping it there →
[Argo CD](/docs/devops/argocd/)

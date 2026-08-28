---
weight: 6050
title: "GitLab"
description: "The forge that tries to be the whole toolchain — repositories, reviews, CI, registry, and security scanning in one system you can self-manage."
icon: "account_tree"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Gitea](/docs/git/gitea/) and [Forgejo](/docs/git/forgejo/) are small forges you can run
on a spare machine. GitLab is the other end of that spectrum: one system that intends to
cover the entire lifecycle — source, merge requests, CI/CD, container and package
registries, security scanning, environments, and issue boards — with a self-managed
edition that large organisations actually deploy.

That ambition is the reason to choose it and the reason not to. One system means one
place to look and one thing to operate; it also means a deployment with real hardware,
real upgrades, and a real administrator behind it.

## Two ways to get it

**GitLab.com**, the hosted service. The free tier is generous on compute and strict in
one specific way that catches teams out: a private top-level namespace is limited to
**five users**, and going over puts it into a read-only state. Public projects don't have
that limit. Budget roughly 400 CI compute minutes a month and 10 GiB of storage per
project on free.

**Self-managed**, from the Community Edition (free software, MIT-licensed core) or the
Enterprise Edition with a licence. The five-user limit is a GitLab.com policy and does not
apply to self-managed installs — this is the fact worth knowing before you assume the
free tier rules out a bigger team.

```bash
docker run -d --name gitlab -p 80:80 -p 443:443 -p 22:22 \
  --shm-size 256m \
  -v gitlab-config:/etc/gitlab -v gitlab-logs:/var/log/gitlab -v gitlab-data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

Expect it to want several gigabytes of RAM and a minute or two to come up — it's Rails,
Postgres, Redis, Sidekiq, Gitaly, and more behind one image. Configuration lives in
`/etc/gitlab/gitlab.rb`, and `gitlab-ctl reconfigure` applies it.

## What you get beyond a forge

| Area | What's there |
|---|---|
| Code | Repositories, merge requests, approval rules, protected branches, code owners |
| [CI/CD](/docs/devops/gitlab-ci/) | Pipelines in `.gitlab-ci.yml` with self-hosted runners — its own page here |
| Registries | Container images, npm, Maven, PyPI, Helm, Terraform modules |
| Security | Dependency, container, secret, and SAST scanning wired into merge requests |
| Delivery | Environments, review apps, feature flags, deployment approvals |
| Planning | Issues, boards, milestones, epics on paid tiers |

Merge requests are the daily surface and they're the strongest part: pipeline status,
scan findings, review threads, and the deploy environment all attached to one page. That
integration is what teams miss when they leave.

Two features worth knowing exist: **review apps** deploy each merge request to a
temporary environment automatically, and **the API and `glab` CLI** cover essentially
everything the UI does, so automation isn't an afterthought.

## Which forge, honestly

| Situation | Pick |
|---|---|
| Open source, contributors everywhere | GitHub — the network effect is the feature |
| Small team, self-hosted, modest hardware | [Gitea](/docs/git/gitea/) or [Forgejo](/docs/git/forgejo/) |
| Everything in one system, on your own network, with an admin to run it | GitLab |
| Compliance wants source, CI, scanning, and audit in one auditable place | GitLab |

The honest caution about self-managing it: this is not a binary you drop on a VPS and
forget. Backups (repositories **and** the database), upgrade paths that must be taken in
order, and enough RAM are ongoing commitments. If nobody owns that, GitLab.com or a
smaller forge will serve you better — a neglected GitLab is a worse outcome than a
well-kept Gitea.

Much of what makes GitLab attractive on paper — advanced security scanning, epics,
compliance dashboards — lives in the paid tiers. Check which tier the feature you're
choosing it for actually sits in, before the migration rather than after.

## Next

To stage and browse history visually → [lazygit](/docs/git/lazygit/)

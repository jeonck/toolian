---
weight: 8150
title: "Nexus Repository"
description: "One place your builds pull dependencies from and push artifacts to — a caching proxy, a private registry, and a chokepoint you control."
icon: "inventory"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Every build downloads the internet. It pulls from npm, PyPI, Maven Central, Docker Hub
and half a dozen others — which means your CI depends on all of them being up, on rate
limits you don't control, and on nobody yanking a package you rely on.

[Nexus Repository](https://www.sonatype.com/products/sonatype-nexus-repository) puts one
server in front of that. It **proxies** the public registries and caches what you fetch,
**hosts** the artifacts you build yourself, and **groups** both behind a single URL your
builds point at. It's the standard answer in Java shops and works the same way for
everything else.

## Run one

```bash
docker volume create nexus-data
docker run -d --name nexus -p 8081:8081 -v nexus-data:/nexus-data \
  sonatype/nexus3
docker exec nexus cat /nexus-data/admin.password
```

Give it a few minutes and a couple of gigabytes of heap — it's a JVM application. Sign in
at `http://localhost:8081` with `admin` and that password, and change it immediately.

## The three repository types, which is the whole model

| Type | Does |
|---|---|
| **proxy** | Mirrors an upstream registry and caches every artifact it fetches |
| **hosted** | Stores what you publish — your own libraries, images, and builds |
| **group** | One URL that searches several repositories in order |

The arrangement you want is: a proxy of the public registry, a hosted repo for your own
releases (and usually a second for snapshots), and a group containing both. Point every
build at the **group** URL and nothing else. Then internal and external dependencies
resolve the same way, and you can change what's behind that URL without touching a single
project.

```xml
<!-- ~/.m2/settings.xml -->
<mirror>
  <id>nexus</id>
  <mirrorOf>*</mirrorOf>
  <url>https://nexus.example.com/repository/maven-public/</url>
</mirror>
```

```bash
npm config set registry https://nexus.example.com/repository/npm-group/
pip config set global.index-url https://nexus.example.com/repository/pypi-group/simple
```

Formats covered include Maven, npm, PyPI, NuGet, Docker, Helm, Go, RubyGems, apt, yum,
and raw — which is why one Nexus often replaces four separate registries.

## Why it's worth the VM

- **Builds stop depending on the public internet.** Upstream outage, rate limit, or a
  removed package: the cache still has what you built with yesterday.
- **Builds get faster.** The second build in a day pulls from the LAN.
- **Docker Hub rate limits stop mattering** once images come through a proxy.
- **One chokepoint to govern.** What may be pulled, what gets scanned, what is retained.
  Pair it with [Trivy](/docs/devops/trivy/) scanning in CI and you have both halves.
- **Somewhere to publish internal packages** without making them public.

## Know what "free" means here

The free edition was renamed from **OSS** to **Community Edition**, and it now carries
usage limits — a cap on requests per day, and component and storage limits Sonatype
publishes with each release. For a small team it's comfortable; for a large CI fleet it's
worth measuring your actual request volume against the current numbers before you build a
policy around it. Upgrades give a grace period and report your usage, which is the moment
to check.

That's not a reason to avoid it — it's a reason to read the current terms rather than
inherit an assumption from a colleague's 2019 install.

## Alternatives worth weighing

| Option | Fits |
|---|---|
| **Nexus Repository** | Many formats, one server, self-hosted, mature |
| **JFrog Artifactory** | The enterprise incumbent; more features, more licence |
| **Harbor** | Container images and Helm only, but CNCF and excellent at that |
| **Your forge's registry** | [GitLab](/docs/git/gitlab/), [Gitea](/docs/git/gitea/), and GitHub all ship package registries — often enough on their own |
| **Verdaccio / devpi** | One language, tiny footprint |

Start with the registry your forge already gives you. Move to Nexus when you need several
formats in one place, proxy caching for build speed and resilience, or retention and
access policies your forge can't express.

## Two things to set up on day one

- **Cleanup policies.** Snapshots and untagged images grow without limit; a repository
  with no retention rule becomes a disk-space incident. Set them before the disk fills,
  not after.
- **Back up `/nexus-data`.** The blob store *and* the database live there. And check the
  restore once — an artifact repository you can't restore is a build you can't reproduce.

## Next

Building the artifact is half of it. Getting it onto the cluster, and keeping it there →
[Argo CD](/docs/devops/argocd/)

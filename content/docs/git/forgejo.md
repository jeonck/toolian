---
weight: 6040
title: "Forgejo"
description: "The community-owned fork of Gitea — copyleft, run by a non-profit, and the one building federation between forges."
icon: "diversity_3"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Gitea](/docs/git/gitea/) and [Forgejo](https://forgejo.org/) do the same job: a
self-hosted forge with repositories, pull requests, issues, a package registry, and
GitHub-compatible CI, from one small binary. If you've read the Gitea page, you already
know the shape of this one.

What's different is who owns it and where it's going. Forgejo is published by
**Codeberg e.V.**, a German non-profit, under **GPL** — so the project can't be
relicensed by a company, and improvements have to come back. It started as a soft fork of
Gitea in late 2022 over Gitea's move to a company structure, and became a hard fork in
2024.

## Run one

```bash
docker run -d --name forgejo -p 3000:3000 -p 222:22 \
  -v forgejo-data:/data \
  -e FORGEJO__database__DB_TYPE=sqlite3 \
  codeberg.org/forgejo/forgejo:latest
```

Same install screen, same `app.ini`, same `FORGEJO__section__KEY` environment variables.
Releases are quarterly with an LTS line and monthly security patches, which is a friendlier
cadence than it sounds — you can stay on LTS and upgrade deliberately.

CI is **Forgejo Actions**, the same GitHub Actions-compatible runner:

```yaml
# .forgejo/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: docker
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
```

## Federation, the part nobody else is building

The ambition that makes Forgejo more than a licence decision: **forge federation over
ActivityPub**, using the ForgeFed vocabulary. The idea is that an account on one instance
can open an issue or a pull request on another — the fediverse model applied to code
hosting, so self-hosting stops meaning your project is invisible.

Recent releases ship working pieces of this, and it's the right thing to be excited about
and the wrong thing to depend on today. Treat it as opt-in and experimental; run your
instance as if it were standalone, and enjoy federation when it matures.

## Codeberg, if you'd rather not host anything

[Codeberg](https://codeberg.org/) is the non-profit's own public instance: free hosting
for free and open-source projects, no ads, no tracking, funded by membership. It runs
Forgejo, so it's also the easiest way to try the software before installing it — and for
a small FOSS project it's a legitimate alternative to GitHub rather than a compromise.

The trade is honest: a smaller audience than GitHub, and none of the network effects that
come with it. Mirroring to GitHub for discovery while developing on Codeberg is a common
arrangement.

## Choosing between the two forks

| | Forgejo | [Gitea](/docs/git/gitea/) |
|---|---|---|
| Licence | GPL from v9 | MIT, plus a commercial edition |
| Steward | Codeberg e.V., a non-profit | Gitea Ltd and the community |
| CI | Forgejo Actions | Gitea Actions |
| Distinctive work | ActivityPub federation | Enterprise features, commercial support |
| Hosted option | Codeberg, free for FOSS | Gitea Cloud |

Day to day they feel nearly identical, so pick on the two things that actually differ:
**governance** and **licence**. Copyleft and a non-profit steward point to Forgejo; the
MIT licence — which matters if you want to build a closed product on top — or a support
contract point to Gitea.

**Migration runs one way.** Gitea → Forgejo is documented and supported, often little
more than swapping the image and letting migrations run. Going back is not a supported
path, and after the 2024 hard fork the two are no longer drop-in interchangeable. Take a
backup, test the swap on a copy, and treat the decision as a decision.

## Running it in earnest

The operational advice is the same as its sibling's, and worth repeating because it's what
people get wrong: back up `/data` **and** the database together so issues and pull request
discussions survive; put TLS in front and set `ROOT_URL` correctly or clone URLs come out
wrong; and read the upgrade notes before a major version, because migrations run on start.

## Next

To stage and browse history visually → [lazygit](/docs/git/lazygit/)

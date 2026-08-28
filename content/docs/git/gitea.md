---
weight: 6030
title: "Gitea"
description: "A GitHub-shaped forge you host yourself — repositories, pull requests, issues, a registry, and CI, from one small binary."
icon: "dns"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Sometimes the code cannot live on GitHub. A closed network, a client contract, a
regulator, or simply not wanting a company between you and your history — and the
alternative used to be GitLab, which is a real deployment with real hardware behind it.

[Gitea](https://about.gitea.com/) is the small answer: **one Go binary**, an embedded
database if you want one, and repositories, pull requests, issues, wikis, releases, a
package registry, and CI in the interface everyone already knows. It runs comfortably on
a machine you'd otherwise call too small.

## Run one

```bash
docker run -d --name gitea -p 3000:3000 -p 222:22 \
  -v gitea-data:/data \
  -e GITEA__database__DB_TYPE=sqlite3 \
  docker.io/gitea/gitea:latest
```

Open `http://localhost:3000`, complete the install screen, and the first account you
create is the administrator. SQLite is genuinely fine for a small team; move to Postgres
when you have concurrency or a backup story that wants it.

Everything you'd otherwise click lives in `/data/gitea/conf/app.ini` — and every setting
in it has a `GITEA__section__KEY` environment variable, which is what makes it pleasant
in Docker Compose or Kubernetes.

## What you get

| Feature | Notes |
|---|---|
| Repositories, PRs, issues, reviews | The GitHub model, including protected branches and required approvals |
| **Gitea Actions** | GitHub Actions-compatible workflows; most marketplace actions run unchanged |
| Package registry | npm, PyPI, Maven, Docker, Helm and more, built in |
| Mirroring | Pull from or push to GitHub, so it can be a warm backup rather than a migration |
| Webhooks and API | An API close enough to GitHub's that scripts port easily |
| SSO | LDAP, OAuth2, SAML in recent versions |

Mirroring is the underrated one: point Gitea at your GitHub repositories and it keeps a
synchronised copy. That's an evening of work and a genuine answer to "what if we lose
access to our account."

## CI, without a second system

```bash
docker run -d --name act-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -e GITEA_INSTANCE_URL=http://gitea:3000 \
  -e GITEA_RUNNER_REGISTRATION_TOKEN=<token> \
  gitea/act_runner:latest
```

```yaml
# .gitea/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
```

If that looks exactly like [GitHub Actions](/docs/devops/github-actions/), that's the
point — the workflow syntax is the same and the common actions work. Two caveats worth
knowing before you plan a migration: actions are fetched from GitHub by default (mirror
them locally for an air-gapped install), and the more exotic GitHub-hosted features have
no equivalent.

## Gitea or Forgejo?

Forgejo forked from Gitea in 2022 over governance — Gitea's move to a company structure —
and the two have since diverged enough that they're no longer drop-in interchangeable.

| | Gitea | Forgejo |
|---|---|---|
| Licence | MIT, with a commercial edition | GPL from v9 onward |
| Governance | Gitea Ltd plus community | Codeberg e.V., community-owned |
| CI | Gitea Actions | Forgejo Actions — same idea |
| Distinctive work | Enterprise features | ActivityPub federation between forges |

For most self-hosters either is a good answer and the day-to-day experience is nearly
identical. Choose **Forgejo** if community governance and a copyleft licence matter to
you; **Gitea** if you want the MIT licence or a commercial support option. Decide once —
migrating later is possible but no longer trivial.

## Running it in earnest

- **Back up `/data` and the database together.** A repository backup without the issues
  and pull requests loses the discussion, which is often the more valuable half.
- **Put TLS in front of it** — a reverse proxy with [cert-manager](/docs/devops/cert-manager/)
  or Caddy, and set `ROOT_URL` correctly or clone URLs come out wrong.
- **Keep SSH on its own port** and decide early between the built-in SSH server and the
  host's; mixing them is a common source of confusion.
- **Watch the upgrade notes.** Migrations run on start, so take the backup first.

## Next

To stage and browse history visually → [lazygit](/docs/git/lazygit/)

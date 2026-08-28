---
weight: 8720
title: "gitleaks"
description: "Catching an API key before it reaches Git history — and finding the ones already there."
icon: "password"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Committing a secret is a five-second mistake with a long tail. It's in the history, in
every clone, in every fork, and possibly in a search index within minutes — and deleting
the line changes nothing, because Git remembers.

[gitleaks](https://github.com/gitleaks/gitleaks) is a single Go binary that scans for
credentials: in your working tree, in staged changes, and through the entire history of a
repository.

## Install and scan

```bash
brew install gitleaks        # or a release binary, or the Docker image

gitleaks detect --source . --verbose        # scan history
gitleaks detect --no-git --source .         # scan files as they are on disk
gitleaks protect --staged                   # scan what you're about to commit
```

`gitleaks detect` on an old repository is worth running once today. It takes a minute and
it is the fastest way to find out whether the answer to "have we ever committed a key" is
yes.

## Stop the next one at the commit

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.28.0
    hooks:
      - id: gitleaks
```

```bash
pre-commit install
```

That's the whole integration, and it's where the tool belongs — see
[pre-commit hooks](/docs/git/pre-commit/) for the framework. A hook can be skipped with
`--no-verify`, so add a CI job too:

```yaml
- uses: gitleaks/gitleaks-action@v2
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Belt and braces: the hook gives fast feedback, the CI job is the one that can't be
bypassed.

## Tuning, without switching it off

```toml
# .gitleaks.toml
[extend]
useDefault = true

[[rules]]
id = "internal-service-token"
description = "Our own token format"
regex = '''svc_[a-z0-9]{32}'''

[allowlist]
description = "Test fixtures with fake values"
paths = ['''testdata/.*''', '''.*_test\.go''']
```

Two habits keep the signal high: add a rule for **your own** credential formats — the
default rules know AWS and Stripe, not your internal token scheme — and allowlist by path
with a comment, never by lowering the whole configuration.

## When it finds something real

Order matters, and most people get it backwards:

1. **Rotate the credential first.** Assume it's compromised the moment it was pushed. This
   is the only step that actually protects anything.
2. **Then remove it from the code** and commit the fix.
3. **Rewriting history is optional and rarely worth it.** `git filter-repo` or BFG can
   purge it, but everyone must re-clone, and the secret may already be copied elsewhere.
   Rewriting a public repository's history does not un-leak anything.
4. **Check the access logs** for use of the credential between the push and the rotation.

The mistake to avoid is reversing 1 and 2 — quietly deleting the line, feeling relieved,
and leaving a live key in the history.

## Alternatives

TruffleHog additionally *verifies* candidate secrets by calling the provider, which cuts
false positives sharply; GitHub's own secret scanning with push protection is free for
public repositories and excellent if you're on GitHub. Running gitleaks locally plus your
platform's scanning server-side is a sensible pair — they catch different things.

## Next

Once the artifact is clean, prove it's yours → [Sigstore](/docs/security/sigstore/)

---
weight: 8710
title: "Semgrep"
description: "Static analysis with patterns that look like the code they match — thousands of ready rules, and your own in ten minutes."
icon: "policy"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Traditional static analysis is either a linter that knows nothing about security or an
enterprise scanner that takes an hour and returns a PDF. [Semgrep](https://semgrep.dev/)
sits in between: it runs in seconds, ships thousands of community rules, and — the part
that changes what you do with it — lets you write your own rules in a syntax that looks
like the code they match.

The engine is open source under LGPL 2.1, needs no account, and has no usage limit. The
hosted platform adds cross-file analysis and triage, free for small teams.

## Run it

```bash
pipx install semgrep          # or: brew install semgrep

semgrep --config auto .                      # community rules matched to your stack
semgrep --config p/security-audit .
semgrep --config p/owasp-top-ten .
semgrep --config p/secrets .
```

The first run on a real repository takes a minute and returns more than you want. Narrow
it before you judge it:

```bash
semgrep --config p/owasp-top-ten --severity ERROR --error .
```

## Writing a rule, which is the actual reason to use it

Every codebase has rules no off-the-shelf scanner knows: don't call the internal HTTP
client without a timeout, never log the request body, always use the repository layer
rather than raw SQL. Semgrep expresses those as patterns.

```yaml
# .semgrep/no-raw-sql.yml
rules:
  - id: no-raw-sql-in-handlers
    languages: [python]
    severity: ERROR
    message: Use the repository layer; raw SQL in a handler bypasses tenant filtering.
    patterns:
      - pattern: $CONN.execute("...")
      - pattern-inside: |
          def $HANDLER(request, ...):
            ...
    fix: # optional autofix
```

```bash
semgrep --config .semgrep/ .
```

`$VAR` is a metavariable, `...` matches anything, and `pattern-inside` scopes the match to
a context. If that feels familiar, it's the same idea as
[ast-grep](/docs/files/ast-grep/) — Semgrep aims at security and ships the rule library;
ast-grep aims at refactoring and is faster for bulk rewrites.

The moment this earns its place is after an incident: write the rule that would have
caught it, commit it, and the class of bug is closed for everyone rather than remembered
by one reviewer.

## In CI, on the diff

```yaml
- uses: semgrep/semgrep-action@v1
  with:
    config: >-
      p/owasp-top-ten
      .semgrep/
```

Scan **changed files** on pull requests and run the full scan on a schedule. A backlog of
findings in old code should never block today's merge — same discipline as
[SonarQube](/docs/devops/sonarqube/)'s new-code gate.

## Managing false positives

```python
# nosemgrep: no-raw-sql-in-handlers  -- migration script, no tenant context
```

Annotate with a reason, or add a `paths: exclude:` block in the rule. Never silence by
lowering severity globally — that hides the true positives too. If a rule is wrong more
often than right, fix the rule; that's the advantage of rules you own.

## What it doesn't do

Pattern matching finds patterns. It won't reason about whether your authorisation model is
coherent, and cross-file data-flow analysis is where the paid platform sits. Pair it with
[Trivy](/docs/devops/trivy/) for dependencies — most vulnerabilities arrive through
packages, not through code you wrote.

## Next

The finding with the shortest path to a breach → [gitleaks](/docs/security/gitleaks/)

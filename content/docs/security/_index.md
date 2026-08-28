---
weight: 87
title: "Security"
description: "Finding problems in your code, secrets, dependencies, running app, and hosts — with tools a developer can run, not a separate department."
icon: "shield"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Security tooling has a reputation for arriving as a PDF from another team, six weeks after
the code shipped. The tools here don't work that way: each one runs on your machine or in
your pipeline, in seconds, and tells you something specific enough to fix today.

They line up along the path a change takes.

| Stage | Tool | Finds |
|---|---|---|
| While you write | [Semgrep](/docs/security/semgrep/) | Vulnerable patterns in source, and your own rules |
| Before you commit | [gitleaks](/docs/security/gitleaks/) | Credentials about to enter Git history |
| When you build | [Trivy](/docs/devops/trivy/) | Known CVEs in dependencies, images, and IaC |
| When you publish | [Sigstore](/docs/security/sigstore/) | Nothing — it *proves* the artifact is yours |
| Against the running app | [ZAP](/docs/security/zap/) | What an attacker sees from outside |
| On the hosts, continuously | [Wazuh](/docs/security/wazuh/) | Intrusions, file changes, compliance drift |

Three more live in [Containers & Deploys](/docs/devops/) because that's where they're
operated: [Trivy](/docs/devops/trivy/) for scanning,
[Falco](/docs/devops/falco/) for runtime detection in Kubernetes,
[secrets management](/docs/devops/secrets/) for credentials at rest, and
[policy as code](/docs/devops/policy-as-code/) for what the cluster will accept.

## The order that actually works

Turning everything on at once produces a wall of findings, and a wall of findings gets
ignored. A sequence that sticks:

1. **Secret scanning first.** It's the cheapest to run, has almost no false positives, and
   a leaked credential is the finding with the shortest path to a real breach.
2. **Dependency scanning next**, filtered to *fixable* issues. Most vulnerabilities you
   have came in through a package, not through your code.
3. **SAST on new code only.** Gate the diff, not the backlog — the same rule that makes
   [SonarQube](/docs/devops/sonarqube/) work.
4. **Everything else as a scheduled job**, reported to a human, not as a build gate.

And one rule under all of it: **a gate that fires constantly gets bypassed.** Tune first,
enforce second. A tool people route around protects nothing at all.

## Where this stops

These find known patterns and known vulnerabilities. They do not find design flaws,
broken authorisation logic, or the thing your specific business would most regret — that
needs someone thinking about your system. Treat the tooling as the floor, not the ceiling,
and get a real review before anything that handles money or personal data goes live.

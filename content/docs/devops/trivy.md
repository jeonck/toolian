---
weight: 8210
title: "Trivy"
description: "Scanning images, dependencies, and infrastructure code for known vulnerabilities and misconfigurations, in one command."
icon: "security"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Your application code is a small fraction of what you ship. The rest is a base image, a
few hundred transitive dependencies, and some Terraform that opened a security group
wider than you meant. [Trivy](https://trivy.dev/) scans all of it — one binary, Apache
2.0, from Aqua Security.

## Install and scan

```bash
brew install trivy
# or: docker run aquasec/trivy image <name>

trivy image nginx:1.29
trivy fs .                    # dependencies in the current project
trivy repo https://github.com/example/app
trivy config ./terraform      # misconfigurations in IaC
trivy k8s --report summary    # a running cluster
```

The first run downloads a vulnerability database; after that it's fast enough to sit in
a pre-push hook.

## Reading the output without drowning

The first scan of a real image returns hundreds of findings, most of which you will
never act on. Cut it down in this order:

```bash
# only what's fixable, at the severities you'd actually act on
trivy image --severity HIGH,CRITICAL --ignore-unfixed nginx:1.29
```

`--ignore-unfixed` is the important flag. A CVE with no released fix is not a task; it's
a thing to know about. Filtering to fixable findings turns the list into work you can do
today.

Then, for the ones you've assessed and accepted, record the decision in `.trivyignore`
with a reason and a date rather than lowering the threshold:

```
# CVE-2024-XXXXX  base image only, not reachable from our code — review 2026-12
CVE-2024-XXXXX
```

## In CI

```yaml
- name: Scan the image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: ${{ env.IMAGE }}
    severity: HIGH,CRITICAL
    ignore-unfixed: true
    exit-code: '1'          # fail the build
```

Turn `exit-code: 1` on **after** you've cleaned up the existing findings, not before —
a gate that's red on day one gets bypassed by day three, and then it protects nothing.

Trivy also emits an SBOM, which is increasingly something customers and auditors ask for:

```bash
trivy image --format cyclonedx --output sbom.json myapp:1.4.2
```

## The fix is usually upstream

Most findings in a container image come from the base image, not your code. Two changes
remove more vulnerabilities than any amount of triage:

- **Use a smaller base.** `-slim`, Alpine, or a distroless image ships fewer packages,
  and a package you don't ship can't have a CVE.
- **Rebuild regularly.** An image built six months ago carries six months of unpatched
  system packages. A weekly rebuild of an unchanged app is not busywork.

Pair it with `pip-audit`, `npm audit`, or Dependabot on the language side, and with
[secrets scanning](/docs/git/pre-commit/) in your hooks. Trivy detects secrets too
(`trivy fs --scanners secret .`), which is a useful backstop when a hook was skipped.

## Next

Scanning covers what you ship; something has to watch what it does →
[Falco](/docs/devops/falco/)

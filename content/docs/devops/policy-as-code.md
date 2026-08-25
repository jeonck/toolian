---
weight: 8100
title: "Policy as Code"
description: "Kyverno and OPA Gatekeeper — rules the cluster enforces itself, instead of a wiki page nobody reads."
icon: "gavel"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

"Always set resource limits." "Never pull `latest` in production." "Every workload needs
an owner label." Every organisation has this list, and every organisation discovers it
was optional the day something without limits evicts the thing that mattered.

Policy as code makes the cluster enforce the list. An admission controller inspects every
object before it is persisted and **rejects** it, **mutates** it, or records a violation.
The rules live in Git and are reviewed like everything else.

## Kyverno, if you're on Kubernetes

[Kyverno](https://kyverno.io/) writes policies as Kubernetes resources — YAML matching
YAML, no new language. It graduated in the CNCF in 2026 and is the shortest path from
"we should enforce this" to it being enforced.

```bash
helm repo add kyverno https://kyverno.github.io/kyverno
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-resource-limits
spec:
  validationFailureAction: Audit        # start here, switch to Enforce later
  rules:
    - name: check-limits
      match:
        any:
          - resources:
              kinds: [Pod]
      validate:
        message: "Every container needs CPU and memory limits."
        pattern:
          spec:
            containers:
              - resources:
                  limits:
                    memory: "?*"
                    cpu: "?*"
```

Kyverno also **mutates** — adding a default label, injecting a sidecar, setting
`imagePullPolicy` — and **generates**, creating a NetworkPolicy or a ConfigMap whenever a
namespace appears. That last one quietly removes a lot of onboarding toil.

## OPA and Gatekeeper, if policy is bigger than the cluster

[Open Policy Agent](https://www.openpolicyagent.org/) is a general policy engine with its
own language, Rego, and Gatekeeper is its Kubernetes admission controller. Rego is harder
than YAML, and it buys you one thing worth having: the *same* engine and the same policies
across Kubernetes, Terraform plans, CI pipelines, and application authorisation.

Rough rule: **Kyverno** if the policies are about Kubernetes objects, **OPA** if you need
one policy language across several systems, and if you're unsure, Kyverno — you can add
OPA later for the cases it doesn't cover.

## Audit first, always

The one deployment mistake that matters here is enforcing on day one. A `deny` policy on
a live cluster breaks deployments that were fine yesterday, and the fastest fix anyone
finds is deleting the policy.

1. Run every new policy in **Audit** mode and read the report for a week.
2. Fix the workloads it flags, or add explicit exclusions with a reason.
3. Switch to **Enforce**, starting with the least critical namespace.
4. Leave a documented exception path — `kube-system` and some operators legitimately need
   things your policy forbids.

## What's worth enforcing first

| Policy | Prevents |
|---|---|
| Resource requests and limits required | One workload starving a node |
| No `:latest` tags | "It worked yesterday" with no way to say what changed |
| Required labels (owner, team) | Nobody knowing whose thing broke |
| No privileged containers, no hostPath | The obvious container escapes |
| Images from approved registries only | An unreviewed image reaching production |

Pair this with [Trivy](/docs/devops/trivy/) in CI: scanning catches the vulnerable image
before it ships, admission control catches whatever reaches the cluster anyway.

## Next

To automate the deploy itself → [GitHub Actions](/docs/devops/github-actions/)

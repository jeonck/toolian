---
weight: 8560
title: "Chaos Engineering"
description: "Breaking things on purpose, in daylight, with Chaos Mesh or LitmusChaos — so the first test of your failover isn't the real one."
icon: "bolt"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Every system has a set of beliefs written into it: the retry will handle a blip, the
replica will take over, the queue will absorb the spike. Most of those beliefs have never
been tested, and the test eventually happens anyway — at 3am, unannounced, with customers
watching.

Chaos engineering runs the test on purpose: inject a failure deliberately, in working
hours, with the people who know the system watching, and find out whether the belief holds
while a rollback is one command away.

## It starts as an experiment, not a tool

The discipline matters more than the software. Every experiment has four parts:

1. **A hypothesis.** "If one of the three API pods is killed, error rate stays under 0.1%
   and p99 latency stays under 500ms." Not "let's see what happens."
2. **The smallest blast radius that tests it.** One pod, one namespace, staging first.
3. **A stop condition, agreed in advance.** What number means abort, and who calls it.
4. **A result you write down.** Held, or didn't — and if it didn't, a fix and a re-run.

You can run the first several with no tooling at all: `kubectl delete pod`, a firewall
rule, `stress-ng` on a node. Reach for a tool when you want experiments to be repeatable,
scheduled, and scoped safely.

## Chaos Mesh and LitmusChaos

Both are CNCF projects that express experiments as Kubernetes resources.

```bash
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm install chaos-mesh chaos-mesh/chaos-mesh -n chaos-mesh --create-namespace
```

```yaml
apiVersion: chaos-mesh.org/v1alpha1
kind: NetworkChaos
metadata: { name: api-latency, namespace: staging }
spec:
  action: delay
  mode: one                      # one pod, not all of them
  selector:
    namespaces: [staging]
    labelSelectors: { app: payments-api }
  delay:
    latency: "300ms"
    jitter: "50ms"
  duration: "5m"                 # always set this
```

The failure types worth trying, roughly in order of what actually breaks in production:

| Experiment | The belief it tests |
|---|---|
| Pod kill | The replica set recovers and nobody notices |
| Network latency between services | Timeouts and retries are configured sensibly |
| Network partition to a dependency | The circuit breaker opens rather than cascading |
| CPU or memory pressure | Limits and autoscaling do the right thing |
| Clock skew, DNS failure, disk full | The unglamorous ones that cause real outages |

`duration` is not optional. An experiment that doesn't end by itself becomes an incident
when the person running it gets pulled into a meeting.

## The prerequisites nobody mentions

Chaos experiments are only useful if you can see the result and stop the bleeding, so do
these first:

- **Monitoring that would catch it.** If your dashboards can't show the effect of a killed
  pod, the experiment tells you nothing — that's a
  [Prometheus](/docs/observability/prometheus/) and
  [Grafana](/docs/observability/grafana/) gap to fix first.
- **A rollback or kill switch** you have used before, not one you assume works.
- **People told in advance.** An unannounced experiment that pages an unaware on-call
  engineer destroys the trust the practice needs. Announce it, then run it.
- **Staging before production.** Production experiments are the mature end of this, not
  the start.

## What it's really for

The value isn't the broken pod — it's the finding underneath. The retry that hammers a
struggling dependency instead of backing off. The health check that reports healthy while
the service can't reach its database. The failover nobody had run since it was built. Each
is cheap to fix on a Tuesday and expensive to discover at 3am.

## Next

The other thing nobody watches until it's too late — the bill →
[OpenCost](/docs/observability/opencost/)

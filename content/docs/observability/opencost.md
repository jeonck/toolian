---
weight: 8570
title: "OpenCost"
description: "Telling which team, namespace, and workload spent the money — Kubernetes cost allocation you can self-host."
icon: "savings"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

The cloud bill arrives as one number per account. The cluster it describes runs forty
services from six teams, and the invoice has nothing to say about which of them cost what.
So the response to "reduce cloud spend" is guesswork, and it usually lands on whoever
happens to be listening.

[OpenCost](https://opencost.io/) fixes the attribution problem. It's a CNCF project — the
open-source core of Kubecost — that takes cloud pricing and actual pod resource usage and
produces cost per namespace, per deployment, per label, per team.

## Install

```bash
helm repo add opencost https://opencost.github.io/opencost-helm-chart
helm install opencost opencost/opencost -n opencost --create-namespace
```

It needs [Prometheus](/docs/observability/prometheus/) — it reads usage from there — and
it will use your cloud's real rates, including discounts and spot pricing, if you give it
billing-export access. Without that it falls back to public list prices, which are still
useful for *relative* comparisons between teams.

```bash
kubectl port-forward -n opencost svc/opencost 9003
curl "http://localhost:9003/allocation/compute?window=7d&aggregate=namespace" | jq
```

There's a Grafana dashboard for it, which is where this belongs — cost next to latency and
error rate, on the same screen, owned by the same people.

## The number that matters is efficiency, not spend

The first look is always spend by namespace, and it's rarely the useful one — the biggest
namespace is often the most important service, legitimately. The number to look at is
**requests versus actual usage**:

- A pod requesting 2 CPUs and using 0.1 is holding a node's worth of capacity nobody can
  schedule onto. That's the money.
- Multiply by every replica and every environment, and over-requesting is usually the
  single largest line of waste in a cluster.
- Under-requesting shows up too — throttled pods and evictions — and is worth fixing for
  reliability, not cost.

Right-sizing requests against a fortnight of observed usage typically moves the bill more
than any architectural change, and it takes an afternoon. Vertical Pod Autoscaler in
recommendation mode gives you the numbers to argue with.

## The other three easy wins

| Waste | Fix |
|---|---|
| Idle staging clusters overnight and at weekends | Scale to zero on a schedule |
| Abandoned namespaces from finished projects | An owner label and a quarterly sweep |
| Persistent volumes outliving their workloads | Alert on unattached PVs |

## Make it a habit, not a project

Cost work fails when it's a one-off panic. Two things make it stick:

- **Showback, monthly.** Each team sees their own number and its trend. Not a chargeback
  process with invoices — just visibility, which changes behaviour on its own.
- **An owner label enforced at admission.** Cost data you can't attribute to a team is
  a report nobody acts on; [policy as code](/docs/devops/policy-as-code/) is how the
  label stops being optional.

One caution: this is Kubernetes compute. Managed databases, object storage, egress, and
SaaS bills live outside it and are frequently the larger half — your cloud provider's cost
explorer still owns that view.

## Next

Watching what the system does is one half; watching what could be done to it is the
other → [Security](/docs/security/)

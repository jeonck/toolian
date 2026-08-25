---
weight: 8090
title: "Istio"
description: "A service mesh: mutual TLS, retries, traffic splitting, and per-call telemetry without changing application code."
icon: "lan"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Once there are a dozen services, the same problems appear in every one of them: encrypt
traffic between services, retry a failed call, time out sensibly, split traffic for a
canary, and know which caller is hammering which callee. Solve them in code and you solve
them a dozen times, differently, in three languages.

A **service mesh** moves that layer beside the application instead. [Istio](https://istio.io/)
intercepts traffic in and out of each pod, so mTLS, retries, timeouts, traffic shifting,
and golden-signal metrics arrive without a line of application code.

## Ambient mode changed the calculus

Istio's original design put an Envoy sidecar in every pod — powerful, and expensive in
memory and latency, which is why plenty of teams tried it and backed out. **Ambient mode**
went GA in late 2024 and removes the per-pod sidecar: a node-level ztunnel handles mTLS
and L4, and an optional waypoint proxy handles L7 policy only for the namespaces that
need it.

Practically, that means you can adopt it in two steps — encryption and identity first, at
near-zero per-pod cost, then L7 features only where they earn their keep.

```bash
istioctl install --set profile=ambient
kubectl label namespace default istio.io/dataplane-mode=ambient
```

Existing pods need no change and no restart-with-sidecar ritual.

## The three things people actually turn on

**Mutual TLS between services**, without certificates in your code:

```yaml
apiVersion: security.istio.io/v1
kind: PeerAuthentication
metadata: { name: default, namespace: istio-system }
spec:
  mtls: { mode: STRICT }
```

**Traffic splitting** for canaries and blue-green:

```yaml
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata: { name: myapp }
spec:
  hosts: [myapp]
  http:
    - route:
        - destination: { host: myapp, subset: v1 }
          weight: 90
        - destination: { host: myapp, subset: v2 }
          weight: 10
```

**Resilience defaults** — timeouts, retries, outlier ejection — applied uniformly instead
of per-service and per-language.

The by-product is telemetry: request rate, error rate, and latency for every service-to-
service call, exported to [Prometheus](/docs/observability/prometheus/) without
instrumenting anything. For a legacy service nobody wants to touch, that alone can justify
the mesh.

## Istio, Linkerd, or nothing at all

| | Choose |
|---|---|
| Fewest moving parts, lowest overhead, opinionated defaults | **Linkerd** |
| The most features, ambient mode, the largest ecosystem | **Istio** |
| Under ~10 services, one team, no compliance driver | **Neither, yet** |

That last row is the honest one. A mesh is a distributed system you now operate: another
control plane, another failure mode, another thing to upgrade. Below a certain scale,
library-level retries plus [OpenTelemetry](/docs/observability/opentelemetry/) get you
most of the value for a fraction of the operational cost.

Adopt a mesh when a concrete requirement names it: mTLS everywhere for compliance,
traffic shifting for progressive delivery, or per-call telemetry across services you can't
modify.

## If you do adopt it

- **Start with one namespace**, and keep `PeerAuthentication` in `PERMISSIVE` until you've
  confirmed nothing outside the mesh is talking to it.
- **Gateway API is where this is heading.** New ingress work should use `Gateway` and
  `HTTPRoute` rather than the older Istio Gateway CRDs.
- **Budget for upgrades.** Meshes move fast; a version you never upgrade becomes the
  reason you can't upgrade Kubernetes.

## Next

Rules the cluster should enforce on its own →
[Policy as Code](/docs/devops/policy-as-code/)

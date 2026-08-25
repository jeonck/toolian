---
weight: 8070
title: "Kustomize"
description: "Environment differences as patches over plain manifests — no templating language, and it's already in kubectl."
icon: "layers"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Helm](/docs/devops/helm/) solves environment differences by templating: manifests become
Go templates and values fill the holes. Kustomize takes the opposite route — **the
manifests stay plain YAML**, and each environment is a small patch layered over them. No
template language, no `{{ }}`, and `kubectl` already ships it.

## Base and overlays

```
k8s/
  base/
    kustomization.yaml
    deployment.yaml
    service.yaml
  overlays/
    staging/kustomization.yaml
    production/kustomization.yaml
```

```yaml
# base/kustomization.yaml
resources:
  - deployment.yaml
  - service.yaml
commonLabels:
  app: myapp
```

```yaml
# overlays/production/kustomization.yaml
resources:
  - ../../base
namespace: production
namePrefix: prod-

images:
  - name: myapp
    newTag: 1.4.2

replicas:
  - name: myapp
    count: 5

patches:
  - path: resources-patch.yaml
    target: { kind: Deployment, name: myapp }
```

```yaml
# overlays/production/resources-patch.yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: myapp }
spec:
  template:
    spec:
      containers:
        - name: myapp
          resources:
            requests: { cpu: 500m, memory: 512Mi }
            limits: { memory: 1Gi }
```

```bash
kubectl kustomize k8s/overlays/production      # render and read it
kubectl apply -k k8s/overlays/production       # apply it
kubectl diff -k k8s/overlays/production        # what would change
```

Every file above is a manifest you can read on its own. That's the whole appeal: the base
is valid Kubernetes YAML, so your editor's schema validation, `kubectl explain`, and
copy-paste from the docs all still work.

## The features you'll use constantly

| Field | Does |
|---|---|
| `images` | Set the tag without touching the Deployment — how CI promotes a build |
| `replicas` | Scale per environment |
| `configMapGenerator` | Build a ConfigMap from files or literals, with a content hash in the name |
| `secretGenerator` | Same for Secrets — pair with SOPS, never commit plaintext |
| `patches` | Strategic-merge or JSON6902 changes to any field |
| `components` | Optional feature bundles, mixed into the overlays that want them |

The generators' **content hash** is quietly the best feature: change a ConfigMap and its
name changes, which forces the Deployment to roll. Without that, editing a ConfigMap
changes nothing until someone restarts the pods by hand — a classic silent failure.

## Kustomize or Helm?

- **Kustomize** when the manifests are yours and environments differ by a handful of
  fields. Nothing to learn beyond Kubernetes itself.
- **Helm** when you're packaging something for other people to install and configure, or
  consuming third-party charts.

Most real repositories do both: Helm for upstream components, Kustomize for their own
services. And you can layer them — `helm template` a chart, then patch the output — which
is the escape hatch when a chart is almost right.

Both [Argo CD](/docs/devops/argocd/) and [Flux](/docs/devops/flux/) render either
natively, so this stays a local preference rather than a platform decision.

## Two things that bite

- **Patches match by target, silently.** A patch whose `target` matches nothing is not an
  error — it just does nothing. Diff the rendered output when a change doesn't appear.
- **Don't nest overlays deeply.** Base → environment is legible. Base → region → tier →
  environment is a maze; at that point a chart with values is honestly simpler.

## Next

Every cluster needs certificates, and nobody wants to renew them by hand →
[cert-manager](/docs/devops/cert-manager/)

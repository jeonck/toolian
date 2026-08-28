---
weight: 8180
title: "Flux"
description: "GitOps as a set of controllers with no dashboard — the other CNCF option, and when it fits better than Argo CD."
icon: "sync_alt"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Argo CD](/docs/devops/argocd/) and [Flux](https://fluxcd.io/) solve the same problem —
the cluster pulls its desired state from Git and reconciles continuously — and both are
CNCF graduated projects. The difference is shape, not principle.

Argo CD is one application with a web UI at the centre. Flux is a **set of small
controllers** with no UI at all: a source controller that watches Git, OCI registries, or
Helm repositories; a kustomize controller and a helm controller that apply what it finds;
an image automation controller that can write new tags back to Git.

That makes Flux the better fit when the platform is itself automated — everything is an
API object, nothing needs a human at a screen — and Argo CD the better fit when people
want to look at what's deployed.

## Bootstrap

```bash
brew install fluxcd/tap/flux
flux check --pre

flux bootstrap github \
  --owner=my-org --repository=infra \
  --branch=main --path=clusters/production \
  --personal
```

`bootstrap` is Flux's signature move: it commits its **own** manifests into your
repository and then manages itself from there. Upgrading Flux becomes a pull request, and
a rebuilt cluster reconstitutes itself from Git alone.

## Sources and appliers

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata: { name: app, namespace: flux-system }
spec:
  interval: 1m
  url: https://github.com/my-org/app-config
  ref: { branch: main }
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata: { name: app, namespace: flux-system }
spec:
  interval: 10m
  sourceRef: { kind: GitRepository, name: app }
  path: ./overlays/production
  prune: true
  wait: true
  timeout: 5m
  healthChecks:
    - kind: Deployment
      name: myapp
      namespace: production
```

`prune: true` deletes what leaves Git; `wait` plus `healthChecks` makes the reconciliation
fail loudly when the rollout doesn't become healthy, rather than reporting success because
the YAML applied.

```bash
flux get kustomizations           # what's reconciling, and whether it's healthy
flux reconcile source git app     # stop waiting for the interval
flux suspend kustomization app    # pause during an incident
flux logs --follow
```

## Image automation, the feature Argo doesn't have

Flux can watch a registry, pick the newest tag matching a policy, and **commit it back to
your config repository**:

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImagePolicy
spec:
  imageRepositoryRef: { name: myapp }
  filterTags: { pattern: '^main-[a-f0-9]+-(?P<ts>[0-9]+)', extract: '$ts' }
  policy: { numerical: { order: asc } }
```

CI builds and pushes an image; Flux notices, updates the manifest, commits, and rolls it
out. The deploy is still a Git commit — auditable and revertible — but nobody writes it.

## Choosing

| | [Argo CD](/docs/devops/argocd/) | Flux |
|---|---|---|
| Interface | Web UI, per-resource diffs, sync buttons | CLI and CRDs; UI via third parties |
| Multi-tenancy | Projects and RBAC in the app | Kubernetes RBAC and namespaces |
| Image updates | External (Argo Image Updater) | Built in |
| Fits | Teams who want to see and click | Platforms automated end to end |

Both render Helm and Kustomize, both do drift detection and self-healing. Pick by who is
going to operate it — and don't run both against the same cluster paths, because two
controllers reconciling the same objects is a fight neither wins.

## Next

The same pipeline, assembled from AWS services →
[AWS CodePipeline](/docs/devops/aws-codepipeline/)

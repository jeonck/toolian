---
weight: 8040
title: "Helm"
description: "Packaging a Kubernetes application as one versioned unit, and installing it with the values that differ per environment."
icon: "package_2"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

A single Kubernetes app is rarely a single file. It's a Deployment, a Service, an
Ingress, a ConfigMap, a Secret, an HPA — and then the same eight files again for staging
with four values changed. Copy-paste keeps them in sync until the day it doesn't.

[Helm](https://helm.sh/) turns that pile into a **chart**: templated manifests plus a
`values.yaml` of the things that vary. Install it, upgrade it, roll it back, as one unit
with a version number.

## Install and use something

```bash
brew install helm

helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo postgres

helm install my-db bitnami/postgresql --set auth.database=appdb
helm list
helm status my-db
```

Installing someone else's chart is where most people start, and it's a fair trade: a
production-shaped Postgres, Redis, or ingress controller in one command instead of a day
of YAML. Read the chart's values before you trust it in production — defaults are chosen
to work everywhere, not to suit you.

## Your own chart

```bash
helm create my-app        # scaffold with a working example inside
```

```
my-app/
  Chart.yaml          # name, version, appVersion
  values.yaml         # the defaults
  templates/
    deployment.yaml
    service.yaml
    _helpers.tpl      # named templates, e.g. the label block
```

Templates are Go templates over your values:

```yaml
# templates/deployment.yaml
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

Then one file per environment, holding only the differences:

```bash
helm upgrade --install my-app ./my-app -f values-staging.yaml
helm upgrade --install my-app ./my-app -f values-prod.yaml --atomic --wait
```

`--atomic --wait` is the pairing worth memorising: it waits for the rollout and rolls
back automatically if it fails, instead of leaving you half-deployed.

## The commands you'll actually run

| Command | Use |
|---|---|
| `helm template ./my-app -f values-prod.yaml` | Render locally and read the YAML — no cluster involved |
| `helm diff upgrade` (plugin) | See what an upgrade would change before it changes it |
| `helm lint ./my-app` | Catch template errors early |
| `helm history my-app` / `helm rollback my-app 3` | The release history, and the way back |
| `helm upgrade --install` | Idempotent — the form to use in CI |

`helm template` is the one that turns Helm from magic into text. When a deploy does
something surprising, render it and read what actually gets applied.

## Helm or Kustomize?

Kubernetes ships `kustomize` (`kubectl apply -k`), which layers patches over plain
manifests instead of templating them. Rough guide: **Kustomize** when the manifests are
yours and environments differ by a few fields; **Helm** when you're distributing
something for others to configure, or consuming charts from upstream. Plenty of teams
use both — Helm for third-party components, Kustomize for their own apps.

[Argo CD](/docs/devops/argocd/) renders either one, so this choice doesn't lock you out
of GitOps.

## Two failure modes

- **Templating YAML is string manipulation.** Indentation bugs are the tax; `nindent`
  and `helm template` are the antidote.
- **A chart is dependency-shaped.** Pin chart versions in `Chart.yaml`, and treat
  `helm repo update` followed by a blind upgrade as a production change, because it is.

## Next

To automate the deploy itself → [GitHub Actions](/docs/devops/github-actions/)

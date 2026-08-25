---
weight: 8040
title: "Local Kubernetes"
description: "A throwaway cluster on your laptop with kind, k3d, or minikube — and a fast inner loop with Tilt or Skaffold."
icon: "developer_board"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Learning Kubernetes on a shared dev cluster is slow and slightly frightening: every
experiment is visible, and a mistake is someone else's afternoon. A local cluster removes
both problems. It starts in seconds, it's free, and deleting it when you've broken it is
the intended workflow.

## Pick one

| Tool | What it is | Best for |
|---|---|---|
| **kind** | Kubernetes running in Docker containers | CI, and matching a specific cluster version |
| **k3d** | K3s (a light distribution) in Docker | The fastest start and the smallest footprint |
| **minikube** | A VM or container with add-ons | Learning, and the built-in ingress/dashboard add-ons |

Any of the three is fine. kind is the one CI pipelines standardise on, because a cluster
in a GitHub Actions job is one command.

```bash
brew install kind
kind create cluster --name dev
kubectl cluster-info --context kind-dev
kind delete cluster --name dev
```

A config that maps ports and pins the version — the two things you'll want immediately:

```yaml
# kind.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    image: kindest/node:v1.34.0
    extraPortMappings:
      - containerPort: 30080
        hostPort: 8080
  - role: worker
```

```bash
kind create cluster --config kind.yaml
kind load docker-image myapp:dev        # skip a registry entirely
```

`kind load` is the trick worth knowing: build an image locally and push it straight into
the cluster's nodes, no registry involved.

## The inner loop is the real problem

Even locally, *edit → build → push → apply → wait → look* is far too slow to iterate.
Two tools collapse it:

```bash
brew install tilt
tilt up            # watches files, rebuilds, redeploys, streams logs in one UI
```

```python
# Tiltfile
docker_build('myapp', '.', live_update=[sync('./src', '/app/src')])
k8s_yaml(kustomize('k8s/overlays/dev'))
k8s_resource('myapp', port_forwards='8080:8080')
```

`live_update` is the part that changes the feel: for an interpreted language it syncs the
changed files into the running container instead of rebuilding the image, so a save is
visible in about a second. Skaffold does the same job with a YAML config and fits better
where the team already has Google tooling.

## Use it in CI too

```yaml
- uses: helm/kind-action@v1
  with:
    cluster_name: test
- run: |
    kubectl apply -k k8s/overlays/test
    kubectl wait --for=condition=available deploy/myapp --timeout=120s
    ./scripts/smoke-test.sh
```

That's a genuine integration test of your manifests — including that the Deployment
becomes ready, which is the failure a `kubectl apply --dry-run` will never catch.

## What a local cluster won't tell you

It has no cloud load balancer, no real storage classes, no node autoscaling, and none of
your production network policy. It proves the manifests are coherent and the app starts;
it does not prove the thing will survive Tuesday. Keep a staging environment for that.

## Next

Whichever cluster you're pointed at, these are the tools for looking inside →
[kubectl and k9s](/docs/devops/kubernetes/)

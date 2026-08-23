---
weight: 8070
title: "Argo CD"
description: "GitOps for Kubernetes — the cluster pulls its desired state from Git and puts back whatever drifts."
icon: "sync"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Most CI tools deploy by **pushing**: the pipeline holds cluster credentials and runs
`kubectl apply`. That works until someone patches a deployment by hand at 2am, and now
the cluster and the repository disagree with nobody the wiser.

[Argo CD](https://argo-cd.readthedocs.io/) inverts it. A controller runs *inside* the
cluster, watches a Git repository, and continuously reconciles reality against what's
committed. Git is the desired state; anything else is drift, and drift is either shown to
you or silently corrected. That model — **GitOps** — is now the mainstream way to deploy
to Kubernetes: Argo is a CNCF graduated project, and CNCF's own survey puts it in the
majority of respondents' clusters.

## Install

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d

kubectl port-forward -n argocd svc/argocd-server 8080:443
```

The web UI at `https://localhost:8080` is a genuine reason people pick Argo over the
alternatives — a live diff between Git and the cluster, per resource, with the sync
button next to it.

## Your first Application

An `Application` says: this Git path, into this cluster and namespace.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/example/my-app-config.git
    targetRevision: main
    path: k8s/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true        # delete resources removed from Git
      selfHeal: true     # revert manual changes to the cluster
    syncOptions:
      - CreateNamespace=true
```

`prune` and `selfHeal` are the two switches that decide how strict you are. With both on,
`kubectl edit` in production lasts about ninety seconds — which is the point, and also
the thing to warn your team about before you turn it on.

The CLI mirrors the UI:

```bash
argocd app list
argocd app diff my-app        # Git vs cluster, right now
argocd app sync my-app
argocd app history my-app     # and: argocd app rollback my-app <id>
```

## The shape that scales

- **Two repositories.** Application code in one, Kubernetes manifests in another. CI
  builds the image and commits a new tag to the config repo; Argo notices and rolls it
  out. Deploys become a reviewable commit, and rollback is `git revert`.
- **App of apps.** One Application whose Git path contains more Applications, so a whole
  environment is bootstrapped from a single object.
- **ApplicationSet.** Generate Applications from a template — one per cluster, per
  directory, per team — instead of writing forty near-identical YAML files.
- **Argo Rollouts.** A sibling project that swaps the built-in Deployment strategy for
  canary and blue-green releases with automated analysis.

## The two things that trip people up

**Secrets do not go in Git as plaintext.** GitOps means everything is in the repository,
which makes this the first question anyone asks. Use Sealed Secrets, SOPS with age or
KMS, or the External Secrets Operator pulling from Vault or a cloud secret manager —
what's committed is an encrypted or referencing object, never the value.

**Argo CD is not CI.** It doesn't build, test, or push images. You still need
[GitHub Actions](/docs/devops/github-actions/), [GitLab CI](/docs/devops/gitlab-ci/), or
[Jenkins](/docs/devops/jenkins/) in front of it. The handoff is a commit to the config
repository, and that seam is where a deploy pipeline usually needs the most care.

## Is it for you?

It needs Kubernetes, and it pays off with more than one environment or cluster. A single
container on [Railway](/docs/vibe-infra/railway/) does not need a reconciliation loop.
Three environments, several teams, and an auditor asking who deployed what — that's where
the model earns its keep, because the answer is the Git history.

## Next

The same pipeline, assembled from AWS services →
[AWS CodePipeline](/docs/devops/aws-codepipeline/)

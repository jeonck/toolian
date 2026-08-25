---
weight: 8050
title: "kubectl and k9s"
description: "The minimum you need to inspect and debug a Kubernetes cluster."
icon: "hub"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Even if you don't operate Kubernetes yourself, you will end up reading logs from a
deployed service or working out why a pod won't start. This page concentrates on
**reading and debugging**.

## Install

```bash
brew install kubectl k9s
brew install kubectx        # context and namespace switching
```

## Check your context first

```bash
kubectl config get-contexts
kubectl config current-context
kubectx staging             # switch context
kubens my-namespace         # switch namespace
```

**Running a command against production without realising you were pointed at it** is
the most common accident here. Putting the current context in your prompt prevents it
(Starship's `kubernetes` module does this).

## Looking around

```bash
kubectl get pods                      # current namespace
kubectl get pods -A                   # all namespaces
kubectl get pods -o wide              # with node and IP
kubectl get pods -w                   # watch changes live
kubectl get deploy,svc,ingress        # several resource types at once

kubectl describe pod <name>            # details including events (start here)
kubectl get pod <name> -o yaml         # the full definition
```

## Logs and shells

```bash
kubectl logs <pod>                     # logs
kubectl logs -f <pod>                  # follow
kubectl logs <pod> -c <container>      # when there are sidecars
kubectl logs <pod> --previous          # logs from before a restart (crash causes)
kubectl logs -l app=api --tail=100     # several pods by label

kubectl exec -it <pod> -- sh           # into the container
kubectl port-forward svc/api 8080:80   # reach it locally
```

`--previous` is almost always what you need when debugging CrashLoopBackOff.

## Reading pod status

| Status | Meaning and what to check |
|---|---|
| `Pending` | Not scheduled. Resources or node selectors → Events in `describe` |
| `ImagePullBackOff` | Wrong image name or registry credentials |
| `CrashLoopBackOff` | The container keeps dying → `logs --previous` |
| `OOMKilled` | Exceeded the memory limit → adjust limits |
| `Running` but requests fail | Check the readiness probe and the service selector |

## k9s — a terminal dashboard

```bash
k9s
```

| Key | Action |
|---|---|
| `:pods` `:deploy` `:svc` | Switch resource type |
| `/text` | Filter |
| `l` | Logs |
| `d` | Describe |
| `s` | Shell in |
| `Ctrl+D` | Delete |
| `:ctx` / `:ns` | Switch context or namespace |
| `?` | Help |

It collapses the get → describe → logs loop into single keystrokes. If you look at
clusters often, start here rather than with `kubectl`.

## Safety rules

- Always dry-run a delete first: `--dry-run=client`.
- In a production context, run `kubectl diff -f` before `kubectl apply`.
- Editing a live resource with `edit` gets overwritten by the next deploy. Fix the
  manifest repository instead.

## Next

To ship an app onto that cluster as one versioned unit → [Helm](/docs/devops/helm/)

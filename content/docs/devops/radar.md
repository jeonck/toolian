---
weight: 8055
title: "Radar"
description: "A 30 MB Go binary that gives a Kubernetes cluster a topology map, an event timeline, GitOps drift, a 36-check audit, and an MCP server — k9s with a graph."
icon: "radar"
date: "2026-09-14"
lastmod: "2026-09-14"
draft: false
---

[k9s](/docs/devops/kubernetes/) shows you a cluster as lists. [Radar](https://radarhq.io/),
from Skyhook, shows it as a graph: which service talks to which, where traffic flows,
what an ArgoCD app is drifting from. It's a single ~30 MB Go binary that reads your
kubeconfig and opens a web UI — no Electron, no agent, no account — and the same binary
installs in-cluster via Helm when a team wants one shared view.

Open source under Apache 2.0 ([skyhook-io/radar](https://github.com/skyhook-io/radar));
a paid Radar Cloud adds multi-cluster fleet views, SSO/RBAC, routed alerts, and long
event retention.

## Install and run

```bash
brew install skyhook-io/tap/radar        # macOS / Linux
curl -fsSL https://get.radarhq.io | sh   # anywhere
kubectl krew install radar               # as a kubectl plugin

kubectl radar                            # opens http://localhost:9280 on the current context
kubectl radar --kubeconfig ~/.kube/staging.yaml --namespace production --port 9300 --no-browser
```

## What it adds over k9s

| Feature | What it does |
|---|---|
| Topology | Services, workloads, ingresses, and the connections between them, drawn — plus a per-resource "neighbourhood" view |
| Timeline | Keeps events and changes in a local SQLite under `~/.radar/`, past the cluster's default 1-hour event TTL |
| GitOps | ArgoCD and Flux sync status and drift, in the UI, without opening either dashboard |
| Cluster audit | 36 best-practice checks — missing requests/limits, RBAC over-grants, security contexts, probes — labelled by framework |
| Diagnose | One click from a red pod to the events, logs, and owner chain that explain it |
| Helm, jobs, capacity, rightsizing, exec, port-forward | The k9s day-to-day, in a browser |

## The MCP server

This is the part that changes how you use it. The UI process also serves
`http://localhost:9280/mcp`, on by default, so an agent gets the same read of the
cluster you do:

```bash
claude mcp add radar --transport http http://localhost:9280/mcp
```

Read tools include `issues`, `diagnose`, `get_topology`, `get_events`, `get_changes`,
`get_pod_logs`, `get_cluster_audit`, `query_prometheus`; write tools include
`apply_resource`, `patch_resource`, `manage_rollout`, `manage_gitops`, `manage_node`.

"Why is checkout 502ing since 14:00" becomes a question rather than a `kubectl get`
tour. The write tools mean the agent can also roll back the deploy it blames — point it
at staging, not production, and `--no-mcp` turns the endpoint off entirely.

## Where it fits

| Good fit | Poor fit |
|---|---|
| Debugging a service you don't own — the graph is the map | Muscle-memory keyboard driving — k9s is still faster |
| Post-mortems: the timeline survives the cluster's forgetting | Multi-cluster fleets on the free tier — that's Cloud |
| Giving an AI agent read access to a cluster | Air-gapped nodes with no browser |
| A team that wants one shared, in-cluster view | Anyone who already has Lens or Headlamp set up and likes it |

## Next

To ship an app onto that cluster as one versioned unit → [Helm](/docs/devops/helm/)

---
weight: 80
title: "Containers & Deploys"
description: "Containers, Kubernetes and the platform around it, six CI/CD tools, an artifact repository, infrastructure and configuration as code, secrets, scanning, and a service catalogue."
icon: "deployed_code"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

The goal of this category is to retire the phrase *"works on my machine."* It starts
with Docker, which turns an environment into code, moves to Compose for standing up
several services at once, covers the tools for looking inside a running cluster, and
ends with taking humans out of the deploy path entirely.

## Which CI/CD tool, and why teams differ

Five pages here cover deployment automation, and the choice between them is usually made
by the organisation rather than by the tool's features.

| Tool | Chosen when |
|---|---|
| [GitHub Actions](/docs/devops/github-actions/) | The code is on GitHub. One YAML file, no server to run — the default for anything new |
| [Jenkins](/docs/devops/jenkins/) | Builds must run on your own hardware or reach systems no hosted runner can see. Still everywhere in banking, manufacturing, and long-lived infrastructure |
| [GitLab CI/CD](/docs/devops/gitlab-ci/) | Source and pipelines have to live inside your network in one system — the usual on-premise answer |
| [Argo CD](/docs/devops/argocd/) and [Flux](/docs/devops/flux/) | Deploying to Kubernetes, and you want the cluster to pull its state from Git rather than a pipeline pushing into it |
| [AWS CodePipeline](/docs/devops/aws-codepipeline/) | Everything already runs in AWS, and IAM, VPC-internal builds, or an audit trail decide it |

Argo CD is the odd one out: it is deployment only, so it sits *after* one of the others
rather than replacing it. A common pairing is GitHub Actions to build and test, Argo CD
to roll out.

## Containers and clusters

| Tool | Job |
|---|---|
| [Docker](/docs/devops/docker/) and [Compose](/docs/devops/docker-compose/) | Pin an environment; run several services together |
| [Podman](/docs/devops/podman/) | The same containers with no root daemon, and systemd units |
| [Local Kubernetes](/docs/devops/local-kubernetes/) | A throwaway cluster on your laptop, and a fast inner loop |
| [kubectl and k9s](/docs/devops/kubernetes/) | Looking inside a running cluster |
| [Helm](/docs/devops/helm/) / [Kustomize](/docs/devops/kustomize/) | Two ways to handle what differs per environment |
| [cert-manager](/docs/devops/cert-manager/) | TLS issued and renewed without a human |
| [Istio](/docs/devops/istio/) | mTLS, retries, and traffic splitting beside the app |
| [Policy as Code](/docs/devops/policy-as-code/) | Rules the cluster enforces on itself |

## Infrastructure, secrets, and what's watching

| Tool | Job |
|---|---|
| [Terraform](/docs/devops/terraform/) | Declare the cloud resources, review the plan before applying |
| [Pulumi](/docs/devops/pulumi/) | The same model in TypeScript, Python or Go, when the config needs real logic |
| [Ansible](/docs/devops/ansible/) | Bring servers to a described state over SSH, no agent needed |
| [Secrets management](/docs/devops/secrets/) | Get credentials out of `.env` files and repositories |
| [SonarQube](/docs/devops/sonarqube/) | A quality gate on the code you just changed |
| [Nexus Repository](/docs/devops/nexus/) | Proxy the public registries, host your own artifacts |
| [Harbor](/docs/devops/harbor/) | An image registry with scanning, signing, and per-project policy |
| [Trivy](/docs/devops/trivy/) | Scan images, dependencies, and IaC before they ship |
| [Falco](/docs/devops/falco/) | Detect what a container does at runtime, after every scan passed |
| [Backstage](/docs/devops/backstage/) | A catalogue of services, owners, and templates, once there are many |

Then [Observability & Reliability](/docs/observability/) picks it up from the other
side — what the system is doing once it's running.

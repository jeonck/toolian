---
weight: 80
title: "Containers & Deploys"
description: "Pinning environments with Docker, composing services, inspecting Kubernetes, and automating with CI and infrastructure as code."
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
| [Argo CD](/docs/devops/argocd/) | Deploying to Kubernetes, and you want the cluster to pull its state from Git rather than a pipeline pushing into it |
| [AWS CodePipeline](/docs/devops/aws-codepipeline/) | Everything already runs in AWS, and IAM, VPC-internal builds, or an audit trail decide it |

Argo CD is the odd one out: it is deployment only, so it sits *after* one of the others
rather than replacing it. A common pairing is GitHub Actions to build and test, Argo CD
to roll out.

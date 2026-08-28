---
weight: 8160
title: "Harbor"
description: "A container registry you run yourself — with scanning, signing, replication, and quotas built in rather than bolted on."
icon: "anchor"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

A private registry starts as `docker run registry:2` and stays fine until someone asks
which images have critical CVEs, whether the image running in production is the one CI
built, or why a tag was overwritten last Tuesday.

[Harbor](https://goharbor.io/) answers those in the product rather than around it. It's a
CNCF graduated OCI registry with vulnerability scanning, image signing, replication
between instances, per-project RBAC and quotas, and retention rules.

## Install

```bash
curl -LO https://github.com/goharbor/harbor/releases/download/v2.14.0/harbor-offline-installer-v2.14.0.tgz
tar xzf harbor-offline-installer-*.tgz && cd harbor
cp harbor.yml.tmpl harbor.yml     # set hostname and TLS
sudo ./install.sh --with-trivy
```

On Kubernetes there's an official Helm chart. Either way, set the hostname and
certificates properly before the first push — Docker refuses plain HTTP registries, and
retrofitting TLS after images exist is more work than doing it first.

Then it behaves like any registry:

```bash
docker login harbor.example.com
docker tag myapp:1.4.2 harbor.example.com/team-a/myapp:1.4.2
docker push harbor.example.com/team-a/myapp:1.4.2
```

## The features that justify running it

**Scanning, on push.** Harbor ships [Trivy](/docs/devops/trivy/) inside. Every image is
scanned as it arrives, and a project can be set to **block pulls** of images above a
severity threshold — a policy the cluster enforces without any pipeline cooperating.

**Projects with real RBAC.** One project per team or product, with roles, quotas, and its
own policies. Robot accounts give CI a credential that isn't a person's.

**Immutable tags.** Mark `release-*` immutable and nobody can quietly move a tag out from
under a running deployment. This closes the "it worked yesterday and nothing changed"
class of incident.

**Retention rules.** Keep the last ten tags per repository, delete untagged layers on a
schedule. Registries grow without limit otherwise, and disk is the usual first casualty.

**Replication.** Mirror from Docker Hub or another Harbor, on a schedule or on push. Two
uses: a proxy cache so builds stop pulling from the public internet, and pushing approved
images into an air-gapped environment.

**Signing and provenance.** Harbor records signatures, so you can require that only
images signed by your pipeline are deployed — pair it with
[Sigstore and cosign](/docs/security/sigstore/) for the signing itself and
[policy as code](/docs/devops/policy-as-code/) for the admission rule that enforces it.

## Harbor or Nexus?

They overlap, and the difference is scope.

| | [Nexus](/docs/devops/nexus/) | Harbor |
|---|---|---|
| Formats | Maven, npm, PyPI, Docker, and many more | OCI only — images, Helm charts, artifacts |
| Scanning | Via integration | Built in, on push |
| Policy | Basic | Per-project RBAC, quotas, immutable tags, blocking rules |
| Fits | One server for every artifact type | A Kubernetes platform that takes images seriously |

Plenty of platforms run both: Nexus for language packages, Harbor for images. If you only
ship containers, Harbor alone is the cleaner answer — and if your forge's registry already
does the job, keep using it until one of the features above becomes a requirement.

## Two things to decide up front

- **Storage backend.** Local disk works; S3 or [R2](/docs/vibe-infra/cloudflare-r2/)
  scales and simplifies backup. Changing later means moving every blob.
- **Garbage collection is not automatic.** Deleting tags frees nothing until GC runs.
  Schedule it, and know that it needs the registry read-only for the duration in older
  versions.

## Next

Building the artifact is half of it. Getting it onto the cluster, and keeping it there →
[Argo CD](/docs/devops/argocd/)

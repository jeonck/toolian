---
weight: 8080
title: "AWS CodePipeline"
description: "CI/CD assembled from AWS services — worth it when IAM, VPC, and the audit trail matter more than developer comfort."
icon: "cloud_queue"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

If everything you run is already in AWS, there's an argument for keeping the pipeline
there too: builds inside your VPC, permissions as IAM roles rather than long-lived keys
in a third-party settings page, and every action in CloudTrail alongside the rest of your
audit trail.

CodePipeline is the orchestrator. It doesn't build or deploy anything itself — it wires
stages together:

| Service | Role |
|---|---|
| **CodePipeline** | The stage graph: source → build → approve → deploy |
| **CodeBuild** | Runs the build in a managed container, per `buildspec.yml` |
| **CodeDeploy** | Rolls the artifact onto EC2, ECS, or Lambda, with rollback |

## Where the source comes from

Through a **CodeStar connection** to GitHub, GitLab, or Bitbucket — set it up once in the
console, and pipelines reference it by ARN. AWS stopped accepting new CodeCommit
customers in 2024, so for a new pipeline the source stage is an external Git host by
default. (Amazon CodeCatalyst, the newer all-in-one attempt, also closed to new customers
in late 2025 — CodePipeline, CodeBuild, and CodeDeploy are the services AWS points at
now.)

## The build spec

`buildspec.yml` lives in your repository and is the part you'll edit most:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 22
  pre_build:
    commands:
      - aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_REGISTRY
  build:
    commands:
      - npm ci
      - npm test
      - docker build -t $ECR_REGISTRY/$IMAGE_REPO:$CODEBUILD_RESOLVED_SOURCE_VERSION .
  post_build:
    commands:
      - docker push $ECR_REGISTRY/$IMAGE_REPO:$CODEBUILD_RESOLVED_SOURCE_VERSION

artifacts:
  files: [imagedefinitions.json]

cache:
  paths: ['/root/.npm/**/*']
```

Tag images with the commit SHA (`CODEBUILD_RESOLVED_SOURCE_VERSION`), never `latest`. A
pipeline that deploys `latest` cannot tell you what is actually running.

## Two things to set up on day one

**Manual approval before production.** A stage with an approval action pauses the
pipeline and posts to SNS; a named human clicks, and the click is recorded. For a lot of
organisations this single feature is why the pipeline lives in AWS.

**Least-privilege roles.** CodeBuild gets a service role, CodeDeploy gets another, and
the pipeline gets a third. The tempting shortcut is one role with broad permissions;
the correct version is each role scoped to the resources that stage touches. Define them
in [Terraform](/docs/devops/terraform/) or CDK rather than clicking — pipeline IAM is
exactly the kind of thing that drifts.

## Cost and comfort

Billing is roughly a dollar per active pipeline per month plus CodeBuild by the minute,
which for most teams is small. The real cost is developer experience: fewer ready-made
integrations than [GitHub Actions](/docs/devops/github-actions/), a slower edit-and-see
loop, and debugging that means reading CloudWatch logs.

So choose it deliberately. **Yes** when workloads are AWS-only and IAM, VPC-internal
builds, or a compliance story decide the argument. **No** when you mostly want to run
tests on a pull request and ship — a GitHub Actions workflow with an OIDC role into AWS
gives you keyless deploys and a much better day, and is the common middle path: build
and test on GitHub, deploy into AWS with a short-lived role.

## Next

Whatever runs the pipeline, the infrastructure underneath it should be code →
[Terraform](/docs/devops/terraform/)

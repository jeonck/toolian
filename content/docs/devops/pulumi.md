---
weight: 8205
title: "Pulumi"
description: "Infrastructure as code in a real programming language, with stacks, encrypted config, and a preview before every change."
icon: "code_blocks"
date: "2026-09-04"
lastmod: "2026-09-04"
draft: false
---

HCL runs out of room the moment you want a loop with a condition inside it, or to share
a helper with the application code. Pulumi keeps the same model as Terraform — desired
state, a diff, an apply — but writes it in TypeScript, Python, Go, C#, Java, or plain
YAML, so types, tests, and your editor's autocomplete come along.

## Install

```bash
brew install pulumi/tap/pulumi
# or
curl -fsSL https://get.pulumi.com | sh

pulumi version
```

## The loop

```bash
pulumi new aws-typescript   # scaffold a project
pulumi preview              # what would change
pulumi up                   # apply it (shows the preview and asks first)
pulumi refresh              # reconcile state with what's really out there
pulumi destroy              # tear the stack down (careful)
```

`pulumi up` prints the preview and waits for confirmation, so the Terraform
"never apply without reading the plan" rule is built into the command.

## A first program

```typescript
import * as aws from "@pulumi/aws";
import * as pulumi from "@pulumi/pulumi";

const config = new pulumi.Config();
const env = config.require("env");

const assets = new aws.s3.Bucket("assets", {
    bucket: `myapp-assets-${env}`,
    tags: {
        Environment: env,
        ManagedBy: "pulumi",
    },
});

export const bucketName = assets.id;
```

Resource properties come back as `Output<T>` — a value that isn't known until the
resource exists. You can't read one directly; you chain it.

```typescript
const url = assets.bucket.apply(name => `https://${name}.s3.amazonaws.com`);
const arn = pulumi.interpolate`arn:aws:s3:::${assets.bucket}`;
```

That `apply` is the piece people trip over first. Reach for `pulumi.interpolate` when
you're only building a string.

## Stacks instead of directories

One project, one stack per environment — no copied `main.tf` per folder.

```bash
pulumi stack init dev
pulumi stack init prod
pulumi stack select prod
pulumi config set aws:region ap-northeast-2
pulumi config set env prod
pulumi config set dbPassword s3cr3t --secret
```

Settings land in `Pulumi.<stack>.yaml`. Values set with `--secret` are stored encrypted,
so that file is safe to commit — unlike a `.tfvars`.

## State

State lives in Pulumi Cloud by default (free for individuals), with locking and history
included. If you'd rather host it yourself:

```bash
pulumi login s3://myorg-pulumi-state
pulumi login --local          # ~/.pulumi on this machine only
```

`.gitignore`:

```
node_modules/
.pulumi/
```

## Coming from Terraform

- `pulumi convert --from terraform` translates existing HCL as a starting point.
- Terraform providers can be used directly, so a provider without a native Pulumi
  package isn't a blocker.
- Existing resources come in with `pulumi import`, same idea as `terraform import`.

```bash
pulumi import aws:s3/bucket:Bucket assets myapp-assets-prod
```

## Terraform or Pulumi

| Pick Terraform when | Pick Pulumi when |
|---|---|
| The team already reads HCL fluently | The team writes TypeScript or Python daily |
| You want the largest pool of examples and modules | The config needs real loops, conditions, and reuse |
| Config is mostly static declarations | You want to unit-test infrastructure logic |
| Ops owns the infrastructure alone | Application developers own their own resources |

Neither is a mistake. What is a mistake is running both against the same resources.

## Working practices

- **Put `pulumi preview` in CI on every PR** and post the output — the reviewer needs to
  see the diff, the same as a Terraform plan.
- **Keep the language boring.** Infrastructure code that needs a class hierarchy to read
  is worse than the HCL it replaced.
- **Pin the provider versions** in `package.json` / `requirements.txt`, or a provider
  release will change your diff on a quiet afternoon.
- **A renamed resource is a replacement.** Use an explicit resource name or an alias
  when you rename, rather than letting the diff delete and recreate.

## Next

Terraform and Pulumi make the machine; something has to configure it →
[Ansible](/docs/devops/ansible/)

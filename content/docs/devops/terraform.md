---
weight: 8100
title: "Terraform"
description: "Declaring cloud resources as code and reviewing the plan before anything changes."
icon: "architecture"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Infrastructure clicked together in a console is infrastructure nobody can explain six
months later. Terraform states the desired shape as code, works out the difference from
reality, and applies it.

## Install

```bash
brew install terraform
brew install opentofu        # the open-source fork; the command is tofu
```

## The loop

```bash
terraform init         # download providers, configure the backend
terraform fmt          # format
terraform validate     # check syntax
terraform plan         # preview what will change
terraform apply        # apply it
terraform destroy      # tear everything down (careful)
```

**Never `apply` without reading a `plan`.** That single habit prevents most incidents.

## A first configuration

```hcl
terraform {
  required_version = ">= 1.9"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.region
}

variable "region" {
  type    = string
  default = "ap-northeast-2"
}

resource "aws_s3_bucket" "assets" {
  bucket = "myapp-assets-${var.env}"

  tags = {
    Environment = var.env
    ManagedBy   = "terraform"
  }
}

output "bucket_name" {
  value = aws_s3_bucket.assets.id
}
```

## State

Terraform records what actually exists in `terraform.tfstate`.

- **Never commit it.** Secrets can end up in it in plain text.
- On a team, keep it in a remote backend with locking.

```hcl
terraform {
  backend "s3" {
    bucket         = "myorg-tfstate"
    key            = "prod/terraform.tfstate"
    region         = "ap-northeast-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

`.gitignore`:

```
*.tfstate
*.tfstate.*
.terraform/
*.tfvars
```

## Separating environments

Splitting by directory is the clearest approach.

```
infra/
  modules/
    network/
    database/
  envs/
    dev/
      main.tf
      terraform.tfvars
    prod/
      main.tf
      terraform.tfvars
```

Factor shared configuration into modules and vary only the per-environment variables.

```hcl
module "network" {
  source     = "../../modules/network"
  cidr_block = "10.0.0.0/16"
  env        = "prod"
}
```

## Working practices

- **Attach the plan output to the PR.** Reviewers need to see what will change.
- **Read every `destroy` line carefully.** A renamed resource often means a replacement.
  `terraform state mv` avoids that.
- **Pin versions.** Without a `~>` constraint on providers, your plan output changes on
  its own one day.
- **Don't touch the console.** If someone did, the next plan will try to undo it —
  either encode the change or `import` it.

```bash
terraform import aws_s3_bucket.assets myapp-assets-prod
```

## Next

Terraform makes the machine; something has to configure it →
[Ansible](/docs/devops/ansible/)

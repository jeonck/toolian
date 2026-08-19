---
weight: 8050
title: "Terraform"
description: "클라우드 리소스를 코드로 선언하고, 변경 전에 계획을 확인하는 IaC 도구."
icon: "architecture"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

콘솔에서 클릭으로 만든 인프라는 6개월 뒤 아무도 왜 그렇게 되어 있는지 모릅니다.
Terraform은 원하는 상태를 코드로 적고, 실제와의 차이를 계산해 적용합니다.

## 설치

```bash
brew install terraform
brew install opentofu        # 오픈소스 포크 (명령은 tofu)
```

## 기본 흐름

```bash
terraform init         # 프로바이더 내려받기, 백엔드 설정
terraform fmt          # 포맷 정리
terraform validate     # 문법 검증
terraform plan         # 무엇이 바뀔지 미리 보기
terraform apply        # 적용
terraform destroy      # 전부 삭제 (주의)
```

**`plan` 없이 `apply`하지 않습니다.** 이 습관 하나가 사고의 대부분을 막습니다.

## 첫 구성

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

## 상태 파일

Terraform은 `terraform.tfstate`에 "지금 실제로 무엇이 있는지"를 기록합니다.

- **절대 커밋하지 않습니다.** 시크릿이 평문으로 들어갈 수 있습니다.
- 팀으로 쓴다면 원격 백엔드에 두고 잠금을 겁니다.

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

## 환경 분리

디렉터리로 나누는 방식이 가장 명확합니다.

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

공통 구성을 모듈로 빼고 환경별 변수만 다르게 줍니다.

```hcl
module "network" {
  source     = "../../modules/network"
  cidr_block = "10.0.0.0/16"
  env        = "prod"
}
```

## 실무 요령

- **`plan` 결과를 PR에 붙입니다.** 리뷰어가 무엇이 바뀌는지 볼 수 있어야 합니다.
- **`destroy` 표시를 주의 깊게 봅니다.** 이름 변경이 리소스 교체로 이어지는 경우가
  많습니다. `terraform state mv`로 회피할 수 있습니다.
- **버전을 고정합니다.** 프로바이더 버전을 `~>`로 묶지 않으면 어느 날 갑자기
  plan 결과가 달라집니다.
- **콘솔에서 손대지 않습니다.** 손댔다면 `terraform plan`이 되돌리려 하므로,
  코드에 반영하거나 `import`합니다.

```bash
terraform import aws_s3_bucket.assets myapp-assets-prod
```

## 다음 단계

인프라까지 코드가 됐다면 이제 개인 작업을 자동화할 차례입니다 →
[자동화 & 단축키](/docs/automation/)

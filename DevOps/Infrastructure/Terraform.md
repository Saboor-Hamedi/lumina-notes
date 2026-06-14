---
id: a1b2c3d4-1066-4000-8000-000000000066
title: Terraform
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001066
---
# Terraform

Terraform is an Infrastructure as Code (IaC) tool by HashiCorp that uses declarative configuration to provision and manage infrastructure across cloud providers.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Provider | Cloud/service plugin (AWS, Azure, GCP) |
| Resource | Infrastructure component (VM, bucket, DB) |
| Module | Reusable group of resources |
| State | Map of real-world resources to config |
| Plan | Preview of changes before applying |
| Apply | Execute the planned changes |

## HCL (HashiCorp Configuration Language)

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "data" {
  bucket = "my-app-data-${var.environment}"
  tags = {
    Environment = var.environment
  }
}

variable "environment" {
  type    = string
  default = "dev"
}

output "bucket_arn" {
  value = aws_s3_bucket.data.arn
}
```

## Workflow

```
terraform init        # Initialize providers and modules
terraform plan        # Preview changes (safe, read-only)
terraform apply       # Execute changes
terraform destroy     # Tear down infrastructure
```

## State Management

| Backend | Description |
|---------|-------------|
| Local | File on disk (default, not for teams) |
| S3/GCS/Azure Storage | Remote state with locking |
| Terraform Cloud | Managed, collaborative |
| Consul | Key-value store backend |

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/network/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"  # state locking
    encrypt        = true
  }
}
```

## Modules

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]

  enable_nat_gateway = true
  enable_vpn_gateway = false
}
```

## Terraform vs Alternatives

| Tool | Language | Approach | State |
|------|----------|----------|-------|
| Terraform | HCL | Declarative | Remote backends |
| Pulumi | Python/TS/Go/Go | Declarative + imperative | Managed backends |
| AWS CDK | Python/TS | Imperative compiles to CloudFormation | CloudFormation state |
| Ansible | YAML | Imperative (procedural) | No state (stateless) |

## Best Practices

- Use remote state with locking for team collaboration
- Structure by environment (`dev/`, `staging/`, `prod/`) and component
- Pin provider and module versions
- Use `terraform fmt` for consistent formatting
- Run `terraform plan` in CI pipelines (approval gates)
- Store secrets in a vault (never in config files)
- Use workspaces or directories for environment separation
- Write modules for reusable infrastructure patterns

## Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| State file leaked | Encrypt, restrict access, use remote backend |
| Manual changes drift | Import resources, use `refresh` |
| Circular module deps | Restructure, decompose |
| Large state files | Split by component, use `terraform state rm` |

**Links**: [[Infrastructure as Code]] | [[Cloud Computing]] | [[CI CD Pipelines]] | [[Docker Containers]] | [[Kubernetes Basics]]

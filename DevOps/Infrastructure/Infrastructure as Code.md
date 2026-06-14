---
id: new-004-0000-0000-0000-000000000004
title: Infrastructure as Code
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000004
---
# Infrastructure as Code

**Links**: [[Cloud Computing]] | [[Docker Containers]] | [[CI CD Pipelines]] | [[Environment Variables]] | [[Dev Environment Setup]]

## What is IaC?

Infrastructure as Code (IaC) manages cloud resources through declarative or imperative configuration files rather than manual processes. It brings version control, review, and automation to infrastructure.

## Declarative vs Imperative

| Approach | How | Tools |
|----------|-----|-------|
| **Declarative** | Declare desired state | Terraform, CloudFormation, Pulumi |
| **Imperative** | Write step-by-step commands | Ansible, Chef, Puppet |

## Terraform Example

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "storage" {
  bucket = "my-app-storage-2024"
  tags = {
    Name        = "Application Storage"
    Environment = "production"
  }
}

resource "aws_db_instance" "database" {
  engine         = "postgres"
  instance_class = "db.t3.micro"
  db_name        = "myapp"
  username       = "admin"
  password       = var.db_password
  skip_final_snapshot = true
}
```

## Workflow

```bash
terraform init          # Initialize providers
terraform plan          # Preview changes
terraform apply         # Apply changes
terraform destroy       # Tear down resources
```

## Best Practices

- Store state remotely (S3, Terraform Cloud) — never locally
- Use workspaces or directories for environments (dev/staging/prod)
- Pin provider versions
- Keep secrets out of state files
- Review infrastructure changes in pull requests

**Next**: [[Monitoring and Observability]] — Watch your systems
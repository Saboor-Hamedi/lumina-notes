---
id: a1b2c3d4-1104-4000-8000-000000000104
title: Packer
language: markdown
tags: [devops, infrastructure, packer, image-building]
selection: null
isPinned: false
timestamp: 1781500001104
---

**Links**: [[Terraform]] | [[Vagrant]] | [[Infrastructure as Code]] | [[Cloud Computing]] | [[Ansible]] | [[Shell Scripting]]


# Packer

Packer is a tool for creating identical machine images (AMI, Docker, Vagrant, VMDK) for multiple platforms from a single source configuration.

## Why Packer?

| Benefit | Description |
|---------|-------------|
| Immutable infrastructure | Pre-baked images with no post-deploy config |
| Multi-platform | One config → AWS, GCP, Azure, VMware, Docker |
| Versioned images | Images are artifacts with versions |
| Faster deployments | No software installation at boot time |
| Consistency | Same image across dev, staging, production |

## Architecture

```
Packer Template (HCL/JSON)
    │
    ├── Source Builder
    │   ├── amazon-ebs (AWS AMI)
    │   ├── googlecompute (GCP)
    │   ├── docker (Container)
    │   └── virtualbox-iso (Local VM)
    │
    ├── Provisioners (run during build)
    │   ├── shell
    │   ├── ansible
    │   ├── chef
    │   ├── file
    │   └── powershell
    │
    └── Post-Processors
        ├── compress
        ├── vagrant (box)
        ├── docker-tag
        └── manifest
```

## HCL Template

```hcl
packer {
  required_plugins {
    amazon = {
      version = ">= 1.0.0"
      source  = "github.com/hashicorp/amazon"
    }
  }
}

source "amazon-ebs" "ubuntu" {
  ami_name      = "myapp-${var.app_version}-{{timestamp}}"
  instance_type = "t3.medium"
  region        = "us-east-1"
  source_ami_filter {
    filters = {
      name                = "ubuntu/images/*ubuntu-22.04*-amd64-server-*"
      root-device-type    = "ebs"
      virtualization-type = "hvm"
    }
    most_recent = true
    owners      = ["099720109477"]
  }
  ssh_username = "ubuntu"
  tags = {
    Name      = "myapp-${var.app_version}"
    BuiltBy   = "packer"
    BuildDate = "{{isotime}}"
  }
}

build {
  sources = ["source.amazon-ebs.ubuntu"]

  provisioner "shell" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx nodejs python3",
    ]
  }

  provisioner "file" {
    source      = "app/"
    destination = "/opt/myapp/"
  }

  provisioner "shell" {
    script = "scripts/configure.sh"
  }

  post-processor "manifest" {
    output     = "manifest.json"
    strip_path = true
  }
}
```

## Variables

```hcl
variable "app_version" {
  type    = string
  default = "1.0.0"
}

variable "aws_region" {
  type    = string
  default = "us-east-1"
}
```

```bash
packer build -var "app_version=1.2.0" -var "aws_region=eu-west-1" template.pkr.hcl
packer build -var-file=production.pkrvars.hcl template.pkr.hcl
```

## Provisioners

| Provisioner | Use Case |
|-------------|----------|
| shell | Simple commands/scripts |
| ansible | Complex configuration management |
| file | Copy files to the image |
| powershell | Windows provisioning |
| chef | Chef cookbook execution |
| puppet | Puppet manifests |

## Build Lifecycle

```
validate → Initialize plugins → source (create instance) → provision → post-process → cleanup
```

```bash
packer init template.pkr.hcl    # download plugins
packer fmt template.pkr.hcl     # format HCL
packer validate template.pkr.hcl # check syntax
packer build template.pkr.hcl   # build image
```

## Multi-Platform Build

```hcl
source "amazon-ebs" "ubuntu" {
  # AWS config
}

source "googlecompute" "ubuntu" {
  # GCP config
}

source "virtualbox-iso" "ubuntu" {
  # Local VM config
}

build {
  sources = [
    "source.amazon-ebs.ubuntu",
    "source.googlecompute.ubuntu",
    "source.virtualbox-iso.ubuntu",
  ]

  provisioner "shell" {
    # Same provisioning for all platforms
    script = "bootstrap.sh"
  }
}
```

## Best Practices

- Keep images minimal (install only what's needed)
- Version your images semantically
- Use `-timestamp` in AMI names for uniqueness
- Store templates in version control
- CI pipeline validates and builds images
- Clean up old images regularly
- Use `force_deregister = true` for updates to existing AMI names

**Links**: [[Terraform]] | [[Immutable Infrastructure]] | [[Docker Containers]] | [[Infrastructure as Code]] | [[CI CD Pipelines]]

---
id: a1b2c3d4-1105-4000-8000-000000000105
title: Vagrant
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001105
---

**Links**: [[Packer]] | [[Terraform]] | [[Dev Environment Setup]] | [[Ansible]] | [[Infrastructure as Code]] | [[Shell Scripting]]


# Vagrant

Vagrant manages reproducible development environments using VirtualBox, Docker, VMware, or AWS. It shares the same provisioning (shell, Ansible, Chef) as production via Vagrantfile.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Box | Base image (Ubuntu, CentOS, custom) |
| Vagrantfile | Ruby DSL for VM configuration |
| Provider | Hypervisor (VirtualBox, Docker, VMware) |
| Provisioner | Setup tool (shell, Ansible, Puppet) |
| Synced Folder | Share host directory with VM |

## Vagrantfile

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.box_version = "20250418.0.0"

  # Network
  config.vm.network "private_network", ip: "192.168.56.10"
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Synced folder
  config.vm.synced_folder "./app", "/var/www/app",
    type: "nfs",
    mount_options: ["rw", "vers=4", "tcp"]

  # Provider config
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
    vb.name = "myapp-dev"
  end

  # Provisioning
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y nginx nodejs postgresql-client
    systemctl enable nginx
  SHELL

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.extra_vars = {
      environment: "development",
      app_version: "1.0.0"
    }
  end
end
```

## Multi-Machine Setup

```ruby
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo shared provisioning"

  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/jammy64"
    web.vm.network "private_network", ip: "192.168.56.10"
    web.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end
  end

  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/jammy64"
    db.vm.network "private_network", ip: "192.168.56.11"
    db.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
    end
  end
end
```

## Common Commands

```bash
vagrant init ubuntu/jammy64       # create Vagrantfile
vagrant up                        # start VM(s)
vagrant ssh                       # SSH into VM
vagrant status                    # show VM state
vagrant halt                      # stop VM
vagrant destroy                   # delete VM
vagrant reload                    # restart + re-provision
vagrant provision                 # re-run provisioning
vagrant package                   # create a box from current VM
vagrant box list                  # list downloaded boxes
```

## Provisioners

| Provisioner | When to Use |
|-------------|-------------|
| shell | Simple setup scripts |
| Ansible | Complex automation, reusable |
| Puppet/Chef | Enterprise config management |
| Docker | Run containers instead of full VM |
| File | Copy files to VM |

## Docker Provider

```ruby
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.image = "ubuntu:22.04"
    d.has_ssh = true
    d.cmd = ["/sbin/init"]
  end

  # Provision still works
  config.vm.provision "shell", inline: "apt-get update && apt-get install -y nginx"
end
```

## Box Management

```bash
# Add a box
vagrant box add ubuntu/jammy64

# List boxes
vagrant box list

# Remove old versions
vagrant box prune

# Custom box URL
config.vm.box_url = "https://example.com/boxes/myapp.box"
```

## Best Practices

- Use versioned boxes (pin box_version)
- Use environment variables for secrets
- Keep Vagrantfile in version control
- Use `.vagrant.d/` for local overrides
- Use `vagrant package` to distribute dev environments
- Match provisioner to production deployment tool

**Links**: [[Dev Environment Setup]] | [[Docker Containers]] | [[Virtualization]] | [[Ansible]] | [[Terraform]]

---
id: a1b2c3d4-1096-4000-8000-000000000096
title: Ansible
language: markdown
tags: [devops, infrastructure, ansible, configuration-management]
selection: null
isPinned: false
timestamp: 1781500001096
---

**Links**: [[Terraform]] | [[Vagrant]] | [[Packer]] | [[Infrastructure as Code]] | [[Shell Scripting]] | [[Dev Environment Setup]]


# Ansible

Ansible is an agentless automation tool for configuration management, application deployment, and task automation over SSH.

## Agentless Architecture

```
Control Node (Ansible)
     │
     ├── SSH ──→ Managed Host 1 (no agent)
     ├── SSH ──→ Managed Host 2
     └── SSH ──→ Managed Host 3
```

| Aspect | Ansible | Puppet/Chef |
|--------|---------|-------------|
| Agent | None (SSH/WinRM) | Agent on every node |
| Language | YAML (playbooks) | Ruby DSL |
| Push/Pull | Push | Pull (agent polls master) |
| Idempotent | Yes | Yes |
| Learning curve | Low | Medium-High |

## Inventory

```ini
# inventory.ini
[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com ansible_user=admin

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

## Playbook

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  vars:
    nginx_port: 80
    app_version: "1.2.3"

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Deploy configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: restart nginx

    - name: Start service
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

## Modules

| Module | Purpose |
|--------|---------|
| apt/yum/dnf | Package management |
| copy/template | File deployment |
| service/systemd | Service management |
| command/shell | Run arbitrary commands |
| git | Git operations |
| docker_container | Docker management |
| uri | HTTP requests |
| user/group | User management |
| mysql_postgresql | Database operations |

## Roles

```yaml
roles/
  common/
    tasks/main.yml
    handlers/main.yml
    templates/
    files/
    vars/main.yml
    defaults/main.yml
  nginx/
    tasks/main.yml
    templates/nginx.conf.j2
```

```yaml
# playbook.yml
- hosts: all
  roles:
    - common
    - nginx
    - monitoring
```

## Jinja2 Templates

```jinja2
# nginx.conf.j2
server {
    listen {{ nginx_port }};
    server_name {{ inventory_hostname }};

    location / {
        proxy_pass http://127.0.0.1:{{ app_port }};
        proxy_set_header Host $host;
    }
}
```

## Best Practices

- Use `--check` mode to dry-run before applying
- Keep playbooks idempotent (run multiple times safely)
- Use roles for reusable components
- Store secrets in Ansible Vault (encrypted YAML)
- Pin Ansible and collection versions
- Use tags to run specific subsets of tasks

```bash
ansible-playbook -i inventory.ini site.yml --check
ansible-playbook -i inventory.ini site.yml --tags "nginx"
ansible-vault create secrets.yml
ansible-playbook -i inventory.ini site.yml --ask-vault-pass
```

**Links**: [[Terraform]] | [[Infrastructure as Code]] | [[Nginx Configuration]] | [[Docker Containers]] | [[CI CD Pipelines]]

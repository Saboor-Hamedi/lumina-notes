---
id: a1b2c3d4-1159-4000-8000-000000000159
title: Docker Networking and Storage
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001159
---
# Docker Networking and Storage

Docker networking connects containers to each other and the outside world. Docker storage persists data beyond container lifecycle.

## Network Drivers

| Driver | Scope | Use Case |
|--------|-------|----------|
| bridge | Single host | Default, containers on same host |
| host | Single host | No network isolation, performance |
| overlay | Multi-host | Swarm services across hosts |
| macvlan | Multi-host | Assign MAC addresses, direct network |
| none | None | No network, loopback only |
| ipvlan | Multi-host | L2 or L3 networking |

## Bridge Network

```bash
# Default bridge
docker run nginx  # Attached to default bridge (no DNS)

# User-defined bridge (DNS resolution by container name)
docker network create my_network --driver bridge --subnet 172.20.0.0/16

docker run --network my_network --name web -d nginx
docker run --network my_network --name app -d myapp  # Can resolve "web" by name

# Port mapping
docker run -p 8080:80 -p 443:443 nginx
# Host:8080 → Container:80
```

## Overlay Network

```bash
# Requires Swarm mode
docker swarm init
docker network create --driver overlay --attachable my_overlay

# Multi-host communication
# Containers on different hosts can communicate as if on same network
# Encrypted by default (IPsec)
docker network create --driver overlay --opt encrypted=true secure_overlay
```

## DNS and Service Discovery

```bash
# Built-in DNS on user-defined networks
# Container name = hostname = DNS record
# Docker DNS runs on 127.0.0.11

# Custom DNS
docker run --dns 8.8.8.8 --dns-search example.com nginx

# /etc/hosts entries
docker run --add-host host.docker.internal:host-gateway nginx
```

## Network Troubleshooting

```bash
# Inspect network
docker network inspect my_network

# Container network details
docker inspect container_name --format '{{json .NetworkSettings}}'

# Test connectivity from container
docker exec container_name ping other_container
docker exec container_name curl http://web:80

# Capture traffic
docker run -it --net container:target_container nicolaka/netshoot tcpdump
```

## Storage Drivers

| Driver | Overhead | Best For |
|--------|----------|----------|
| overlay2 | Low | Default, all modern Linux |
| aufs | Medium | Legacy systems |
| devicemapper | High | Legacy (loopback slow) |
| btrfs/zfs | Medium | Advanced features |
| vfs | High | Testing only |

## Volume Types

```yaml
# Docker Compose example
services:
  postgres:
    image: postgres:16
    volumes:
      - pgdata:/var/lib/postgresql/data    # Named volume
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql  # Bind mount
      - backups:/backups                    # Named volume
    tmpfs: /tmp                            # In-memory (no persistence)

volumes:
  pgdata:
    driver: local
  backups:
    driver: local
    driver_opts:
      type: nfs
      o: addr=192.168.1.100,rw
      device: :/exports/backups
```

| Type | Persistence | Created By | Use Case |
|------|-------------|------------|----------|
| Named volume | Persistent | `docker volume create` | Best for data persistence |
| Bind mount | Persistent | User | Dev: source code sharing |
| Tmpfs | Ephemeral | Docker | Secrets, temp data |
| Anonymous volume | Container lifecycle | Docker | Caching, build artifacts |

## Named Volume Management

```bash
# Create and inspect
docker volume create app_data
docker volume inspect app_data
# Mountpoint: /var/lib/docker/volumes/app_data/_data

# Backup
docker run --rm -v app_data:/data -v $(pwd):/backup alpine \
    tar czf /backup/app_data_backup.tar.gz -C /data .

# Restore
docker run --rm -v app_data:/data -v $(pwd):/backup alpine \
    tar xzf /backup/app_data_backup.tar.gz -C /data

# Remove unused volumes
docker volume prune
```

## Bind Mounts

```bash
# Source code mount (development)
docker run -v "$(pwd)/src:/app/src" -v "$(pwd)/config:/app/config" myapp

# Mount with read-only
docker run -v "$(pwd)/config:/app/config:ro" myapp

# Mount specific file
docker run -v "$(pwd)/.env:/app/.env" myapp
```

## Storage Performance

```yaml
# Performance tiers:
# tmpfs  (fastest, RAM, limited by memory)
# bind   (native filesystem, direct)
# volume (Docker-managed, thin slightly)
# NFS    (network latency)

# Volume driver plugins:
services:
  app:
    image: myapp
    volumes:
      - data:/data

volumes:
  data:
    driver: rexray/ebs  # AWS EBS
    # driver: netapp     # NetApp NFS
    # driver: flocker    # Flocker
```

## Security

```bash
# Container user permissions
docker run --user 1000:1000 -v "$(pwd)/data:/data" myapp

# Read-only root filesystem
docker run --read-only --tmpfs /tmp myapp

# No new privileges
docker run --security-opt=no-new-privileges myapp

# Capability drop
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myapp

# SELinux/AppArmor labeling
docker run --security-opt label=level:TopSecret myapp
```

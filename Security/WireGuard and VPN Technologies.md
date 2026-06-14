---
id: a1b2c3d4-1108-4000-8000-000000000108
title: WireGuard and VPN Technologies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001108
---
# WireGuard and VPN Technologies

WireGuard is a modern, high-performance VPN protocol designed for simplicity, speed, and cryptographic soundness. It has been included in the Linux kernel since 5.6.

## VPN Protocols Compared

| Protocol | Speed | Security | Complexity | Kerne|
|----------|-------|----------|------------|-------|
| WireGuard | Fastest | Modern (ChaCha20, Curve25519) | Minimal | Kernel-level |
| OpenVPN | Slow (userspace) | Strong | Complex configuration | Userspace |
| IPsec/IKEv2 | Fast | Strong | Very complex | Kernel-level |
| PPTP (deprecated) | Fast | Broken | Simple | ❌ Use anything else |

## WireGuard Architecture

```
WireGuard Interface (wg0)
    │
    ├── Private Key (secret)
    ├── Public Key (shared)
    ├── Listen Port (UDP 51820)
    │
    └── Peers (each has public key + allowed IPs)
```

Key exchange: **Curve25519** (X25519 ECDH)
Encryption: **ChaCha20-Poly1305** (authenticated encryption)
Hashing: **BLAKE2s**

## Server Configuration

```ini
# /etc/wireguard/wg0.conf
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <server-private-key>
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
# Client 1
PublicKey = <client1-public-key>
AllowedIPs = 10.0.0.2/32

[Peer]
# Client 2
PublicKey = <client2-public-key>
AllowedIPs = 10.0.0.3/32
```

## Client Configuration

```ini
[Interface]
PrivateKey = <client-private-key>
Address = 10.0.0.2/24
DNS = 1.1.1.1

[Peer]
PublicKey = <server-public-key>
Endpoint = vpn.example.com:51820
AllowedIPs = 0.0.0.0/0, ::/0  # tunnel all traffic
PersistentKeepalive = 25  # for NAT traversal
```

## Key Generation

```bash
# Generate keys
wg genkey | tee privatekey | wg pubkey > publickey

# Show interface status
sudo wg show

# Quick start/stop
sudo wg-quick up wg0
sudo wg-quick down wg0

# Status output
interface: wg0
  public key: xTIB...dIk=
  private key: (hidden)
  listening port: 51820

peer: 9E8B...F3c=
  endpoint: 203.0.113.5:51820
  allowed ips: 10.0.0.2/32
  transfer: 1.2 GB received, 3.4 GB sent
  latest handshake: 1 minute ago
```

## WireGuard vs OpenVPN Performance

| Test | WireGuard (Kernel) | OpenVPN (TUN) |
|------|-------------------|----------------|
| Throughput (Gigabit) | 940 Mbps | 300-400 Mbps |
| Latency overhead | < 1ms | 2-5ms |
| Connection time | < 100ms (1 RTT) | 1-3 seconds (TLS handshake) |
| Lines of code | ~4,000 | ~100,000+ |

## Use Cases

| Scenario | Why WireGuard |
|----------|--------------|
| Remote access VPN | Fast connection, works on mobile |
| Site-to-site VPN | Simple config, kernel performance |
| Kubernetes pod networking | Low overhead, kernel bypass |
| IoT devices | Minimal resource usage |
| Roaming connections | Handles network changes gracefully |

## Wireshark: TCP over VPN

```
Normal:   [Source: You] → [Destination: Server] (direct)
WireGuard: [Source: You:51820] → [Server:51820] (UDP)
```

No TLS handshake, no TCP-over-TCP issues (which can cause performance problems with OpenVPN).

## Docker Setup

```yaml
services:
  wireguard:
    image: lscr.io/linuxserver/wireguard:latest
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    restart: unless-stopped
```

## Security Considerations

| Practice | Why |
|----------|-----|
| Key management | Keys control access — use short-lived keys if possible |
| AllowedIPs | Restrict what a peer can access (principle of least privilege) |
| Firewall | Only allow UDP 51820 on the server |
| PersistentKeepalive | NAT traversal, but adds overhead |
| Pre-shared key | Optional extra layer of symmetric encryption |

**Links**: [[Computer Networking]] | [[Cryptography Basics]] | [[Kubernetes Basics]] | [[Computer Security]] | [[SSH]]

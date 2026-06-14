---
id: a1b2c3d4-1002-4000-8000-000000000002
title: Computer Networking
language: markdown
tags: [system-design, architecture, networking]
selection: null
isPinned: false
timestamp: 1781500001002
---
# Computer Networking

Computer networking enables communication between devices through protocols, addressing, and data transmission standards.

## OSI Model (7 Layers)

| Layer | Name | Example Protocols |
|-------|------|-------------------|
| 7 | Application | HTTP, FTP, SMTP, DNS |
| 6 | Presentation | TLS/SSL, JPEG, ASCII |
| 5 | Session | RPC, NetBIOS |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP, ICMP, OSPF |
| 2 | Data Link | Ethernet, Wi-Fi, ARP |
| 1 | Physical | Cables, radio, fiber |

## TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | Best-effort |
| Ordering | Preserves order | No ordering |
| Speed | Slower (overhead) | Faster |
| Use cases | Web, email, file transfer | Streaming, VoIP, gaming |

## DNS Resolution

```
Browser → Local Cache → Resolver → Root NS → TLD NS → Authoritative NS → IP
```

## HTTP Versions

| Version | Features |
|---------|----------|
| HTTP/1.1 | Persistent connections, chunked transfer |
| HTTP/2 | Multiplexing, header compression, server push |
| HTTP/3 | QUIC over UDP, zero RTT, improved multiplexing |

## Common Network Topologies

| Topology | Pros | Cons |
|----------|------|------|
| Star | Centralized management, easy troubleshooting | Single point of failure |
| Mesh | High redundancy, fault-tolerant | Expensive cabling |
| Bus | Simple, cheap | Hard to troubleshoot, limited |
| Tree | Scalable, hierarchical | Root failure affects all |

## Key Concepts

- **Subnetting**: Dividing IP networks into smaller segments
- **NAT**: Maps private IPs to a public IP
- **CDN**: Geographical content distribution for low latency
- **Load Balancer**: Distributes traffic across servers

**Links**: [[HTTP Protocol]] | [[Web Security]] | [[Nginx Configuration]] | [[REST API Design]] | [[Cloud Computing]]

---
id: a1b2c3d4-1106-4000-8000-000000000106
title: DNS Deep Dive
language: markdown
tags: [system-design, architecture, dns, networking]
selection: null
isPinned: false
timestamp: 1781500001106
---
# DNS Deep Dive

The Domain Name System (DNS) translates human-readable domain names to IP addresses. It's a hierarchical, distributed database that is foundational to the internet.

## DNS Hierarchy

```
Root (.)
  │
  ├── TLDs (.com, .org, .net, .io, .uk)
  │     │
  │     └── Authoritative (example.com)
  │           │
  │           └── Subdomains (api.example.com, www.example.com)
  │
  └── Root servers (13 logical roots, 1000+ instances via anycast)
```

## Resolution Process

```
Browser → local DNS cache → resolver (ISP/8.8.8.8)
                              ↓
                      Root server → .com TLD → example.com authoritative
                              ↓
                      IP: 93.184.216.34
```

```python
import dns.resolver

# Trace resolution
result = dns.resolver.resolve("example.com", "A")
for ip in result:
    print(f"IPv4: {ip}")

# Check different record types
mx_records = dns.resolver.resolve("example.com", "MX")
for mx in mx_records:
    print(f"Mail: {mx.exchange} (priority {mx.preference})")
```

## Record Types

| Type | Content | Example |
|------|---------|---------|
| A | IPv4 address | `93.184.216.34` |
| AAAA | IPv6 address | `2606:2800:220:1:248:1893:25c8:1946` |
| CNAME | Canonical name (alias) | `www → example.com` |
| MX | Mail server | `mail.example.com priority 10` |
| TXT | Arbitrary text | `v=spf1 include:_spf.google.com ~all` |
| NS | Name server | `ns1.example.com` |
| SOA | Zone authority (start of authority) | admin@example.com serial=2024061301 |
| SRV | Service location | `_sip._tcp.example.com priority 10 weight 5 port 5060` |

## Caching & TTL

```
TTL (Time-to-Live): How long a resolver caches a record.

Short TTL (60-300s): Fast propagation, more queries → load balancing, failover
Long TTL (3600-86400s): Less queries, slower changes → stable records

Recommended:
- A/AAAA records: 300-600s (5-10 min)
- MX records: 3600s (1 hour)
- TXT records: 3600s
- NS records: 86400s (1 day)
```

## Resolver Types

| Type | Audience | Examples |
|------|----------|---------|
| Recursive | End users | 8.8.8.8 (Google), 1.1.1.1 (Cloudflare) |
| Authoritative | Domain owners | ns1.example.com |
| Forwarder | Internal networks | Corporate DNS → external resolver |
| Stub | End device | `systemd-resolved`, `unbound` |

## Common DNS Tools

```bash
# Query resolution
nslookup example.com
dig example.com A
dig +trace example.com  # full resolution path
dig example.com ANY     # all records

# Reverse DNS
dig -x 93.184.216.34

# Check propagation
dig @8.8.8.8 example.com  # query specific resolver
```

## DNSSEC

Cryptographically signs DNS records to prevent spoofing:

```
Resolver → asks for RRSIG → verifies with DNSKEY → DS record at parent zone
```

| Record | Purpose |
|--------|---------|
| DNSKEY | Public key for zone |
| RRSIG | Signature for each record set |
| DS | Hash of DNSKEY, published in parent zone |
| NSEC/NSEC3 | Authenticated denial of existence |

## Split-Horizon DNS

Return different IPs based on the resolver's network:

```
Internal resolver: internal.example.com → 10.0.1.10
External resolver: internal.example.com → 203.0.113.10
```

Used for: corporate networks, Kubernetes services, testing environments.

## HTTP Probe Example

```python
import socket, requests

# Manual DNS + target selection
def resolve_and_request(hostname, path):
    ips = socket.getaddrinfo(hostname, 443)
    for ip_info in ips[:3]:  # try 3 IPs
        ip = ip_info[4][0]
        try:
            session = requests.Session()
            session.trust_env = False
            response = requests.get(
                f"https://{ip}{path}",
                headers={"Host": hostname},
                timeout=5
            )
            return ip, response.status_code
        except:
            continue
    return None, 503
```

**Links**: [[Computer Networking]] | [[HTTP Protocol]] | [[CDN Architecture]] | [[Load Balancing]] | [[Network Security]]

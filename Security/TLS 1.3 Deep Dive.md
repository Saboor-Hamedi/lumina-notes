---
id: a1b2c3d4-1165-4000-8000-000000000165
title: TLS 1.3 Deep Dive
language: markdown
tags: [security, tls, encryption]
selection: null
isPinned: false
timestamp: 1781500001165
---

**Links**: [[Cryptography Basics]] | [[Web Security]] | [[WireGuard and VPN Technologies]] | [[Zero Trust Architecture]] | [[OAuth and Authentication Protocols]] | [[Vault and Secret Management]]


# TLS 1.3 Deep Dive

TLS 1.3 (RFC 8446) is the latest version of the Transport Layer Security protocol. It improves security and performance over TLS 1.2 by reducing handshake latency and removing weak cryptographic options.

## TLS 1.2 vs TLS 1.3

| Feature | TLS 1.2 | TLS 1.3 |
|---------|---------|---------|
| Handshake | 2 RTT (full) | 1 RTT (full), 0-RTT (resume) |
| Cipher suites | Many (30+) | Few (5 AEAD) |
| Key exchange | RSA, DH, ECDH | (EC)DHE only |
| Authentication | Certificate + optional | Certificate + optional |
| Session resumption | Session ID/ticket | PSK + ticket |
| Deprecated | — | RSA key transport, CBC mode, RC4, 3DES, compression, renegotiation |

## Full Handshake (1-RTT)

```
Client                                     Server
  │                                           │
  │── ClientHello ──────────────────────────►│
  │   (key_share, supported_versions,         │
  │    signature_algorithms,                   │
  │    supported_groups)                       │
  │                                           │
  │◄── ServerHello ──────────────────────────│
  │    (key_share, selected_version)           │
  │◄── EncryptedExtensions ──────────────────│
  │◄── Certificate ──────────────────────────│
  │◄── CertificateVerify ────────────────────│
  │◄── Finished ─────────────────────────────│
  │                                           │
  │── Finished ─────────────────────────────►│
  │                                           │
  │◄══════════ Application Data ════════════►│
  │                                           │
  RTT = 1 round trip
```

## 0-RTT (Early Data)

```
Client (has cached PSK)              Server
  │                                      │
  │── ClientHello ────────────────────►│
  │── Early Data (HTTP request) ──────►│
  │   (encrypted with early secret)     │
  │                                      │
  │◄── ServerHello ────────────────────│
  │◄── EncryptedExtensions ────────────│
  │◄── Finished ───────────────────────│
  │                                      │
  │── Finished ───────────────────────►│
  │                                      │
  │◄══════════ Response ══════════════►│
  │                                      │
  RTT = 0 (client sends data immediately)
```

**Replay protection**: 0-RTT data may be replayed. Servers should implement anti-replay mechanisms (e.g., single-use tickets, freshness checks).

## Key Schedule

```
PSK (pre-shared key) or (EC)DHE shared secret
    │
    ▼
Early Secret ──► early_traffic_secret (0-RTT encryption)
    │
    ▼
Handshake Secret ──► handshake_traffic_secret
    │
    ▼
Master Secret ──► application_traffic_secret
                 └── resumption_master_secret (PSK for future sessions)
```

## Cipher Suites (Only 5)

```
TLS_AES_128_GCM_SHA256
TLS_AES_256_GCM_SHA384
TLS_CHACHA20_POLY1305_SHA256
TLS_AES_128_CCM_SHA256
TLS_AES_128_CCM_8_SHA256
```

All use AEAD (Authenticated Encryption with Associated Data) — encryption + authentication in one operation.

## Key Exchange

Only (EC)DHE key exchange. No RSA key transport (which lacked forward secrecy).

```bash
# Supported groups (curves):
# x25519 (default, fastest)
# secp256r1 (P-256)
# secp384r1 (P-384)
# secp521r1 (P-521)

# Key exchange is ephemeral: new key pair per session
# Forward secrecy: compromising long-term key doesn't expose past sessions
```

## Certificate Verification

```bash
# Server sends Certificate + CertificateVerify
# CertificateVerify: signature over handshake transcript
# Signature algorithms:
#   rsa_pss_rsae_sha256 (RSA-PSS, preferred)
#   ecdsa_secp256r1_sha256 (ECDSA)
#   ed25519 (EdDSA)
```

## Session Resumption (PSK)

```bash
Full handshake → Server sends NewSessionTicket → Client caches

Subsequent connection:
  Client: ClientHello + PSK extension
  Server: ServerHello + verify PSK → 0-RTT or 1-RTT handshake

Ticket lifetime: server-configurable (default often 24h)
```

## Implementation

```go
// Go TLS 1.3 (min version enables it)
server := &http.Server{
    Addr: ":443",
    TLSConfig: &tls.Config{
        MinVersion: tls.VersionTLS13,
        // TLS 1.3 automatically selected when client supports it
        // CipherSuites not needed (TLS 1.3 uses fixed set)
    },
}
```

```nginx
# Nginx TLS 1.3
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256;
ssl_ecdh_curve X25519:secp384r1:secp256r1;

# 0-RTT (enable with caution — replay risk)
ssl_early_data on;
```

## Security Improvements

| Removed from TLS 1.3 | Risk |
|----------------------|------|
| RSA key transport | No forward secrecy |
| CBC mode ciphers | Padding oracle attacks (POODLE) |
| RC4 | Biased output, statistical attacks |
| 3DES | Small block size (64-bit) |
| Compression | CRIME/BREACH attacks |
| Renegotiation | Logjam, clientDoS |
| MD5/SHA-224 | Collision attacks |
| Static DH/ECDH | No forward secrecy |

## Performance Comparison

| Metric | TLS 1.2 | TLS 1.3 | Improvement |
|--------|---------|---------|-------------|
| New connection latency (RTT) | 2 | 1 | 2x faster |
| Resumed connection latency | 1 | 0 | Instant |
| Handshake bytes | ~4.5 KB | ~3.5 KB | ~22% less |
| Server CPU (full handshake) | ~0.5ms | ~0.3ms | ~40% less |

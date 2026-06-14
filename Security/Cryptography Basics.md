---
id: a1b2c3d4-1020-4000-8000-000000000020
title: Cryptography Basics
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001020
---
# Cryptography Basics

Cryptography provides confidentiality, integrity, authentication, and non-repudiation for data in transit and at rest.

## Core Goals

| Goal | Meaning | Provides |
|------|---------|----------|
| Confidentiality | Only authorized parties can read | Encryption |
| Integrity | Data hasn't been tampered with | Hashing, MACs |
| Authentication | Verify the sender's identity | Digital signatures |
| Non-repudiation | Sender cannot deny sending | Signatures, audit logs |

## Symmetric Encryption

Same key encrypts and decrypts.

| Algorithm | Key Size | Type | Notes |
|-----------|----------|------|-------|
| AES | 128, 192, 256 | Block (128-bit) | Current standard |
| ChaCha20 | 256 | Stream | Fast in software, mobile-friendly |
| DES (deprecated) | 56 | Block | Broken by brute force |
| 3DES (deprecated) | 112/168 | Block | Slow, superseded by AES |

**Modes**: ECB (avoid — leaks patterns), CBC (needs padding), GCM (authenticated encryption, recommended), CTR (parallelizable)

## Asymmetric Encryption

Key pair: public key encrypts, private key decrypts.

| Algorithm | Key Size | Use Case |
|-----------|----------|----------|
| RSA | 2048, 4096 | Key exchange, signatures |
| ECDSA | 256, 384 | Signatures (smaller keys same security) |
| Ed25519 | 256 | Modern signature (fast, secure) |
| X25519 | 256 | Key exchange (ECDH) |

## Cryptographic Hashing

One-way function: input → fixed-size output (cannot reverse).

| Algorithm | Output | Security |
|-----------|--------|----------|
| SHA-256 | 256 bits | Secure |
| SHA-3 | Variable | Future standard |
| MD5 (broken) | 128 bits | Collision found |
| SHA-1 (broken) | 160 bits | Collision demonstrated |

**Use case**: password storage (use bcrypt/argon2/scrypt, not raw hashes), file integrity, digital signatures.

## Digital Signatures

```
Sign: message → hash → encrypt hash with private key → signature
Verify: message → hash → decrypt signature with public key → compare hashes
```

## TLS Handshake (Simplified)

```
Client → Server: Hello, supported ciphers
Server → Client: Certificate (public key), chosen cipher
Client → Server: Key exchange (encrypted with server's public key)
Both: Derive session key
Client → Server: Finished (encrypted)
Server → Client: Finished (encrypted)
```

## Password Storage

| Method | Secure? | Notes |
|--------|---------|-------|
| Plaintext | ❌ | Never |
| MD5/SHA-1 | ❌ | Fast to brute force |
| bcrypt | ✓ | Cost factor adjustable |
| argon2 | ✓ | Memory-hard, side-channel resistant |
| scrypt | ✓ | Memory-hard, good for HW constraints |

## Common Mistakes

- Writing your own crypto (use standard libraries)
- Hardcoded keys in source code
- Using ECB mode (leaks patterns in ciphertext)
- Not rotating keys
- Storing passwords unsalted
- Using weak RNG (`random()` instead of `secrets` or CSPRNG)

**Links**: [[Web Security]] | [[API Security]] | [[JSON Web Tokens]] | [[OAuth and Authentication Protocols]] | [[Environment Variables]]

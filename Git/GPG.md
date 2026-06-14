---
id: git-033-0000-0000-0000-000000000033
title: Git GPG Signing
language: markdown
tags: [git, gpg, security]
selection: null
isPinned: false
timestamp: 1781500000033
---
# Git GPG Signing

**Links**: [[Commit]] | [[Tag]] | [[Configuration]] | [[Best Practices]]

GPG signing cryptographically verifies that commits and tags came from a trusted source — adding security and authenticity to your project history.

## What is GPG Signing?

GPG (GNU Privacy Guard) signing cryptographically verifies that a commit or tag was created by you, not an imposter.

```
Unsigned:     Jane Doe <jane@example.com>
Signed:       ✅ Jane Doe <jane@example.com> (verified)
```

## Generating a GPG Key

```bash
# Generate a new key
gpg --full-generate-key

# Select:
# - RSA and RSA (default)
# - 4096 bits
# - 1 year expiry (or 0 = never)

# List your keys
gpg --list-secret-keys --keyid-format=long

# Output:
# sec   rsa4096/AAAAAAAAAAAAAAAA 2024-01-15
```

## Configure Git

```bash
# Tell Git your signing key
git config --global user.signingkey AAAAAAAAAAAAAAAA

# Sign all commits by default
git config --global commit.gpgSign true

# Sign all tags by default
git config --global tag.gpgSign true
```

## Signing Commits

```bash
# Sign a commit
git commit -S -m "Signed commit"

# With auto-sign enabled, just commit normally
git commit -m "Auto-signed"
```

## Signing Tags

```bash
git tag -s v1.0.0 -m "Signed release tag"
git tag -a v1.0.0 -m "Release"    # Auto-signed if configured
```

## Adding Key to GitHub

```bash
# Export your public key
gpg --armor --export AAAAAAAAAAAAAAAA

# Copy the output (including BEGIN/END lines)
# Go to GitHub Settings → SSH and GPG keys → New GPG key
# Paste and save
```

## Verification

```bash
# Check commits signed status
git log --show-signature

# Verify a tag
git tag -v v1.0.0
```

**Next**: [[Attributes]] — Configure file behavior
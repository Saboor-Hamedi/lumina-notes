---
id: a1b2c3d4-1072-4000-8000-000000000072
title: Blockchain Fundamentals
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001072
---
# Blockchain Fundamentals

A blockchain is a distributed, immutable ledger that records transactions across a peer-to-peer network without requiring a central authority.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Block | Batch of transactions + header (hash, timestamp, nonce) |
| Chain | Blocks linked by cryptographic hashes |
| Distributed | Full copy on every node |
| Consensus | Agreement mechanism across untrusted nodes |
| Immutable | Past blocks cannot be changed (reorg requires 51%+ hashrate) |
| Smart Contract | Self-executing code on the blockchain |

## Block Structure

```
Block N
┌──────────────────────────────────┐
│ Block Header                     │
│ ├── Previous Block Hash          │
│ ├── Timestamp                    │
│ ├── Nonce                        │
│ ├── Merkle Root (txn hash)       │
│ └── Difficulty Target            │
├──────────────────────────────────┤
│ Transaction List                 │
│ ├── Txn 1 (Coinbase reward)      │
│ ├── Txn 2 (Alice → Bob, 5 BTC)   │
│ └── Txn 3 (Charlie → Dave, 2 BTC)│
└──────────────────────────────────┘
```

## Consensus Mechanisms

| Mechanism | How It Works | Energy | Example |
|-----------|-------------|--------|---------|
| Proof of Work (PoW) | Solve hash puzzle (compute power) | High | Bitcoin, Ethereum (pre-merge) |
| Proof of Stake (PoS) | Validators stake tokens, randomly selected | Low | Ethereum, Solana |
| Delegated PoS (DPoS) | Vote for delegates who validate | Low | EOS, Tron |
| Practical Byzantine Fault Tolerance (PBFT) | Multi-round voting | Low | Hyperledger Fabric |
| Proof of Authority (PoA) | Pre-approved validators | Low | Private chains |

## Smart Contracts (Solidity)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Escrow {
    address public buyer;
    address public seller;
    uint256 public amount;
    bool public isComplete;

    constructor(address _seller) payable {
        buyer = msg.sender;
        seller = _seller;
        amount = msg.value;
    }

    function release() external {
        require(msg.sender == buyer, "Only buyer can release");
        require(!isComplete, "Already complete");
        isComplete = true;
        payable(seller).transfer(amount);
    }

    function refund() external {
        require(msg.sender == buyer, "Only buyer can refund");
        require(!isComplete, "Already complete");
        isComplete = true;
        payable(buyer).transfer(amount);
    }
}
```

## Cryptocurrency vs Blockchain

| Layer | Description | Example |
|-------|-------------|---------|
| Blockchain Protocol | Distributed ledger + consensus | Bitcoin, Ethereum |
| Cryptocurrency | Native token as incentive | BTC, ETH |
| Smart Contracts | Programmable logic on-chain | Uniswap, Aave |
| dApps | Decentralized applications | DEX, NFT marketplace |

## Blockchain Types

| Type | Access | Example |
|------|--------|---------|
| Public | Anyone can read/write | Bitcoin, Ethereum |
| Private | Permissioned access | Hyperledger Fabric |
| Consortium | Multiple orgs govern | R3 Corda |

## Scaling Solutions

| Solution | How | Example |
|----------|-----|---------|
| Layer 1 (on-chain) | Bigger blocks, sharding | Ethereum 2.0 shards |
| Layer 2 (off-chain) | Transactions settled off main chain | Lightning Network, Optimistic Rollups |
| Sidechains | Separate chain pegged to main chain | Polygon, RSK |
| State Channels | Multi-sig off-chain transactions | Lightning Network |

## Use Cases

| Domain | Application |
|--------|-------------|
| Finance | DeFi, stablecoins, remittance |
| Supply chain | Tracking provenance |
| Identity | Self-sovereign identity |
| Healthcare | Patient data access logs |
| Real estate | Property title transfers |
| Voting | Tamper-proof election records |

## Limitations

- **Scalability**: Bitcoin ~7 TPS, Ethereum ~15 TPS (Visa: 24K TPS)
- **Energy**: PoW consumes as much electricity as small countries
- **Irreversibility**: No chargebacks (both pro and con)
- **Complexity**: Hard for non-technical users
- **Regulation**: Unclear legal status globally
- **Security**: Code bugs in smart contracts (DAO hack, $60M)

**Links**: [[Cryptography Basics]] | [[Event Sourcing]] | [[Distributed Systems]] | [[Web Security]] | [[Database Sharding]]

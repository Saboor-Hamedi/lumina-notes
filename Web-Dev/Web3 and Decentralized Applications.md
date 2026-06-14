---
id: a1b2c3d4-1190-4000-8000-000000000190
title: Web3 and Decentralized Applications
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001190
---

**Links**: [[Web Development Fundamentals]] | [[Web Security]] | [[API Gateway Patterns]] | [[GraphQL]] | [[HTTP Protocol]] | [[WebAssembly]]


# Web3 and Decentralized Applications

Web3 refers to decentralized applications (dApps) running on blockchain networks. Users control their own data and assets through cryptographic wallets.

## Architecture Comparison

```
Web2:
  User ──► Frontend ──► API Server ──► Database
  (central authority controls everything)

Web3:
  User ──► Wallet ──► Frontend ──► Smart Contract (blockchain)
     │                    │
     │                    └── IPFS/Arweave (off-chain storage)
     │
     └── Private keys in wallet (user-controlled)
```

## Smart Contracts (Solidity)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Token {
    string public name = "MyToken";
    string public symbol = "MTK";
    uint8 public decimals = 18;
    uint256 public totalSupply;

    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    constructor(uint256 _initialSupply) {
        totalSupply = _initialSupply * 10 ** decimals;
        balanceOf[msg.sender] = totalSupply;
    }

    function transfer(address to, uint256 amount) external returns (bool) {
        require(balanceOf[msg.sender] >= amount, "Insufficient balance");
        balanceOf[msg.sender] -= amount;
        balanceOf[to] += amount;
        emit Transfer(msg.sender, to, amount);
        return true;
    }

    function approve(address spender, uint256 amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address from, address to, uint256 amount) external returns (bool) {
        require(allowance[from][msg.sender] >= amount, "Insufficient allowance");
        require(balanceOf[from] >= amount, "Insufficient balance");
        allowance[from][msg.sender] -= amount;
        balanceOf[from] -= amount;
        balanceOf[to] += amount;
        emit Transfer(from, to, amount);
        return true;
    }
}
```

## ERC Standards

| Standard | Description | Use Case |
|----------|-------------|----------|
| ERC-20 | Fungible tokens | Cryptocurrencies, governance tokens |
| ERC-721 | Non-fungible tokens (NFTs) | Digital art, collectibles |
| ERC-1155 | Multi-token standard | Gaming items (fungible + non-fungible) |
| ERC-4626 | Tokenized vaults | Yield-bearing tokens |

```solidity
// ERC-721 interface
interface IERC721 {
    function balanceOf(address owner) external view returns (uint256);
    function ownerOf(uint256 tokenId) external view returns (address);
    function safeTransferFrom(address from, address to, uint256 tokenId) external;
    function approve(address to, uint256 tokenId) external;
}
```

## dApp Frontend Integration

```javascript
// Connect wallet (ethers.js)
import { ethers } from "ethers";

async function connectWallet() {
    const provider = new ethers.BrowserProvider(window.ethereum);
    const signer = await provider.getSigner();
    const address = await signer.getAddress();
    return { provider, signer, address };
}

// Interact with contract
const contractAddress = "0x1234...";
const abi = [ /* ... */ ];

async function getBalance(signer) {
    const contract = new ethers.Contract(contractAddress, abi, signer);
    const balance = await contract.balanceOf(signer.address);
    return ethers.formatEther(balance);
}

async function transfer(signer, to, amount) {
    const contract = new ethers.Contract(contractAddress, abi, signer);
    const tx = await contract.transfer(to, ethers.parseEther(amount));
    await tx.wait();
    return tx.hash;
}
```

## Development Tools

| Tool | Purpose |
|------|---------|
| Hardhat | Development environment, testing, debugging |
| Foundry | Fast Rust-based Ethereum tooling |
| Remix | Browser-based Solidity IDE |
| OpenZeppelin | Audited contract libraries |
| The Graph | Indexing blockchain data |
| IPFS | Decentralized file storage |

## Gas Optimization

```solidity
// Use uint256 (not uint8) — EVM works in 256-bit words
// Use immutable for constants
// Pack structs tightly (smaller types together)
// Use calldata instead of memory for read-only params
// Batch operations to save gas

struct UserConfig {
    uint128 balance;
    uint64 lastUpdated;
    bool isActive;
}  // Packed into 2 slots instead of 3

// Use events instead of storing data
event UserRegistered(address indexed user, uint256 timestamp);
// Reading events is free (off-chain)
```

## Layer 2 Solutions

| Solution | Type | Throughput | Finality |
|----------|------|------------|----------|
| Optimistic Rollup | L2 (fraud proofs) | ~2k TPS | ~7 days |
| zk-Rollup | L2 (zero-knowledge) | ~2k TPS | Minutes |
| Polygon PoS | Sidechain | ~7k TPS | ~2 min |
| Arbitrum | Optimistic rollup | ~40k TPS | ~7 days |
| StarkNet | zk-rollup | ~100k TPS | Minutes |

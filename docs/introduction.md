# Introduction

## The Problem

Over 70% of Ethereum RPC traffic flows through centralized providers like Infura, Alchemy, and QuickNode. This creates critical issues:

- **No verification** - Applications blindly trust responses
- **Single points of failure** - Provider outages affect millions of users
- **Censorship risk** - Centralized control over blockchain access

## Current Solutions

| Solution | Decentralized | Verified | Simple URL |
|----------|---------------|----------|------------|
| Infura/Alchemy | No | No | Yes |
| Lava/Pocket | Yes | No* | Yes |
| Light Clients | No | Yes | No |
| **DVRPC** | **Yes** | **Yes** | **Yes** |

*Lava and Pocket compare responses from multiple providers but don't cryptographically verify them.

## The DVRPC Approach

DVRPC combines the best of existing solutions:

1. **Integrate existing light clients** - Pluggable architecture, not tied to any single implementation
2. **Generate Merkle proofs** - Cryptographic verification for state and receipt queries
3. **Serve via decentralized P2P network** - No single point of failure
4. **Simple URL** - Drop-in replacement for Infura/Alchemy

The result: verified blockchain data with the same developer experience as centralized providers.

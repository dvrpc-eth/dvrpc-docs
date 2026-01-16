# Introduction

## The Problem

Over 70% of Ethereum RPC traffic flows through centralized providers like Infura, Alchemy, and QuickNode. This creates critical issues:

- **No verification** - Applications blindly trust responses
- **Single points of failure** - Provider outages affect millions of users
- **Censorship risk** - Centralized control over blockchain access
- **Token gates** - Some decentralized alternatives require tokens to use

## Current Solutions

| Solution | Verified | Distributed Routing | No Token |
|----------|----------|---------------------|----------|
| Infura/Alchemy | No | No | No |
| Lava/Pocket | No* | Yes | No |
| Light Clients | Yes | No (local only) | Yes |
| **DVRPC** | **Yes** | **Yes** | **Yes** |

*Lava and Pocket compare responses from multiple providers but don't cryptographically verify them.

## The DVRPC Approach

DVRPC will:

1. **Integrate existing light clients** - Pluggable architecture for consensus verification
2. **Verify and forward proofs** - Cryptographic verification for state and receipt queries
3. **Distribute via P2P network** - Decentralized request routing, no single point of failure
4. **Open access** - No token required, anyone can run a node or use the network

The goal: verified blockchain data with the same developer experience as centralized providers, but without centralization or token requirements.

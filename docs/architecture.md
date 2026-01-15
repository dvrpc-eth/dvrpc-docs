# Architecture

## Components

### 1. Light Client Layer

Pluggable integration with existing light clients (Helios, Nimbus, etc.). Provides verified block headers and state roots without running a full node.

### 2. Proof Engine

Generates cryptographic proofs for responses:
- EIP-1186 state proofs for account and storage queries
- Receipt trie proofs for transaction receipts

### 3. RPC Server

Standard Ethereum JSON-RPC interface. Compatible with existing tools and libraries (ethers.js, web3.js, etc.).

### 4. P2P Network (Future)

- Node discovery
- Header gossip
- Decentralized request routing

## Request Flow

```
Request → RPC Server → Fetch from upstream
                     → Verify against light client state root
                     → Generate/attach proof
                     → Return verified response
```

1. Client sends JSON-RPC request
2. RPC server fetches data from upstream provider
3. Response is verified against light client's state root
4. Merkle proof is generated and attached
5. Verified response returned to client

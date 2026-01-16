# Verification

## Overview

DVRPC verifies blockchain data using cryptographic proofs. The light client provides trusted state roots, and we verify that data matches those roots.

## How Proofs Work

Ethereum stores state in a Merkle-Patricia Trie. Each block header contains a `stateRoot` - a hash that commits to the entire state at that block.

To prove a value exists in state:
1. Start with the trusted state root (from light client)
2. Follow a path through trie nodes to the value
3. Verify each node's hash matches its parent's reference

If the proof verifies, the value is guaranteed to be correct for that block.

## EIP-1186: State Proofs

### What It Is

EIP-1186 defines a standard RPC method `eth_getProof` that returns Merkle proofs for account state and storage.

### How We Use It

We **call** `eth_getProof`, we don't implement it:

1. Client requests `eth_getBalance(address)`
2. We call `eth_getProof(address)` on upstream RPC
3. Upstream returns account data + Merkle proof
4. We verify the proof against light client's state root
5. If valid, return result + proof to client

```
Upstream RPC                    DVRPC Node                     Client
     │                              │                            │
     │  ←── eth_getProof(addr) ──── │                            │
     │                              │                            │
     │  ──── account + proof ────→  │                            │
     │                              │                            │
     │                     verify against                        │
     │                     light client root                     │
     │                              │                            │
     │                              │ ── result + proof ───────→ │
```

### What the Proof Contains

- **accountProof** - Trie nodes from state root to account
- **storageProof** - Trie nodes from storage root to value (optional)
- **balance, nonce, codeHash, storageHash** - Account data

## Receipt Proofs

### The Problem

There is no `eth_getReceiptProof` RPC method. This is a gap we fill.

### How We Build Receipt Proofs

1. Fetch all receipts in the block via `eth_getBlockReceipts`
2. Build the receipt trie ourselves (RLP encoding, Merkle-Patricia structure)
3. Generate Merkle proof for the target receipt
4. Verify against `receiptsRoot` in the block header
5. Return receipt + proof to client

```
Upstream RPC                    DVRPC Node                     Client
     │                              │                            │
     │  ← eth_getBlockReceipts() ── │                            │
     │                              │                            │
     │  ──── all receipts ───────→  │                            │
     │                              │                            │
     │                     build receipt trie                    │
     │                     generate proof                        │
     │                     verify against header                 │
     │                              │                            │
     │                              │ ── receipt + proof ──────→ │
```

No standard RPC provides this.

## Planned Client-Side Verification

Clients receiving DVRPC responses will be able to verify proofs independently:

1. Get the state root for the block (from their own light client, or trust DVRPC's)
2. Verify the Merkle proof against that root
3. Confirm the result matches the claimed value

```
Trusted state root + Proof + Value = Verified ✓
```

## Future: JS Verification Library

We plan to provide a lightweight JavaScript library for client-side verification:

```javascript
import { verifyStateProof, verifyReceiptProof } from '@dvrpc/verify';

const response = await fetch(DVRPC_URL, { ... });
const { result, proof } = await response.json();

// Verify state proof (EIP-1186)
const validState = verifyStateProof(stateRoot, proof, result);

// Verify receipt proof (our format)
const validReceipt = verifyReceiptProof(receiptsRoot, proof, result);
```

This will allow frontends to verify responses without trusting any RPC provider.

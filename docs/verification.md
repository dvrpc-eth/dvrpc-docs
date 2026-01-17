# Verification

## The Trust Problem

When you call `eth_getBalance` on a centralized RPC:

1. Provider returns "5 ETH"
2. You trust the provider is telling the truth
3. No way to verify

Even `eth_getProof` doesn't fully solve this:

1. Provider returns balance + proof + state root
2. You verify proof against state root
3. But state root came from the provider too
4. Still trusting the provider

## How DVRPC Solves This

DVRPC adds **consensus verification**:

1. Light client syncs with beacon chain
2. Sync committee (512 validators) signs each block
3. State root is verified by consensus, not trusted from RPC
4. Proof verified against consensus-signed state root

```
┌─────────────────────────────────────────────────────────────┐
│                    Verification Flow                        │
│                                                             │
│  Beacon Chain                                               │
│       │                                                     │
│       ▼                                                     │
│  Sync Committee signs block                                 │
│       │                                                     │
│       ▼                                                     │
│  Light Client verifies signature                            │
│       │                                                     │
│       ▼                                                     │
│  Trusted State Root                                         │
│       │                                                     │
│       ▼                                                     │
│  Verify account proof against state root                    │
│       │                                                     │
│       ▼                                                     │
│  Data is correct (mathematically proven)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Consensus Proof Fields

| Field | What It Proves |
|-------|----------------|
| stateRoot | The state root for this block |
| slot | Which beacon chain slot this is |
| syncCommitteeBits | Which of 512 validators signed |
| syncCommitteeSignature | Aggregated BLS signature |

## Client-Side Verification

With the proof and consensus data, clients can verify:

1. **Sync committee signature** - Verify BLS signature against known committee
2. **State root authenticity** - State root is signed by consensus
3. **Account proof** - Merkle proof valid against state root
4. **Data integrity** - Balance/storage matches proof

No trust required. Math only.

## What You Need to Verify

To fully verify client-side:

1. Current sync committee public keys (from beacon chain)
2. BLS signature verification
3. Merkle-Patricia trie proof verification

Our JS library will handle this.

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

We call `eth_getProof` on upstream, then verify against our light client's state root:

1. Client requests `eth_getBalance(address)`
2. We call `eth_getProof(address)` on upstream RPC
3. Upstream returns account data + Merkle proof
4. We verify the proof against light client's state root
5. If valid, return result (+ proof if header set)

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

No standard RPC provides this.

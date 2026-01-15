# Verification

## How Proofs Work

Ethereum stores state in a Merkle-Patricia Trie. Each block header contains a `stateRoot` - a hash that commits to the entire state at that block.

To prove a value exists in state:
1. Start with the trusted state root (from light client)
2. Follow a path through trie nodes to the value
3. Verify each node's hash matches its parent's reference

If the proof verifies, the value is guaranteed to be correct for that block.

## EIP-1186 Explained

EIP-1186 standardizes state proofs for Ethereum. A proof contains:

- **accountProof** - Trie nodes from state root to account
- **storageProof** - Trie nodes from storage root to value (optional)
- **balance, nonce, codeHash, storageHash** - Account data

Verification:
1. Hash the account address to get the trie path
2. Walk the accountProof, verifying each node
3. Confirm the leaf contains the claimed account data
4. For storage: repeat with storageProof and storage root

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
import { verifyProof } from '@dvrpc/verify';

const response = await fetch(DVRPC_URL, { ... });
const { result, proof } = await response.json();

const verified = verifyProof(stateRoot, proof, result);
```

This will allow frontends to verify responses without trusting any RPC provider.

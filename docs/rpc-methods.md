# RPC Methods

## Planned Support

| Method | Status | Proof Type |
|--------|--------|------------|
| `eth_getBalance` | Planned | EIP-1186 state proof |
| `eth_getStorageAt` | Planned | State + storage proof |
| `eth_getTransactionReceipt` | Planned | Receipt trie proof |
| `eth_getLogs` | Planned | Receipt proofs |
| `eth_blockNumber` | Planned | Verified header |

## Proof Types

### EIP-1186 State Proof

Proves an account's state (balance, nonce, code hash, storage root) at a specific block. Uses Merkle-Patricia trie proofs against the block's state root.

### Storage Proof

Extends EIP-1186 to prove specific storage slots within a contract. First proves the account exists, then proves the storage value within that account's storage trie.

### Receipt Trie Proof

Proves a transaction receipt exists in a block. Uses the receipts trie root from the block header to verify the receipt's inclusion.

### Verified Header

Returns the block number from the light client's verified chain head. No additional proof needed since the light client already verified the header.

## Response Format

Verified responses will include a `proof` field:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x1234...",
  "proof": {
    "type": "eip1186",
    "accountProof": [...],
    "blockHash": "0x..."
  }
}
```

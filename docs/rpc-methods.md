# RPC Methods

## Planned Support

| Method | Status | Proof Type | How |
|--------|--------|------------|-----|
| `eth_getBalance` | Planned | EIP-1186 state proof | Call `eth_getProof`, verify |
| `eth_getStorageAt` | Planned | State + storage proof | Call `eth_getProof`, verify |
| `eth_getTransactionReceipt` | Planned | Receipt trie proof | Build proof ourselves |
| `eth_getLogs` | Planned | Receipt proofs | Build proofs ourselves |
| `eth_blockNumber` | Planned | Verified header | From light client |

## Proof Types

### EIP-1186 State Proof

Used for: `eth_getBalance`, `eth_getStorageAt`, `eth_getCode`, `eth_getTransactionCount`

We call `eth_getProof` on the upstream RPC and verify the returned proof against the light client's state root. Standard Ethereum proof format.

### Receipt Trie Proof

Used for: `eth_getTransactionReceipt`, `eth_getLogs`

No standard RPC exists for receipt proofs. We fetch all receipts in the block, build the receipt trie, and generate Merkle proofs ourselves. Verified against `receiptsRoot` in the block header.

### Verified Header

Used for: `eth_blockNumber`, `eth_getBlockByNumber`

Returns data directly from the light client's verified chain. No additional proof needed since the light client already verified consensus.

## Response Format

Verified responses will include a `proof` field:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x1234...",
  "proof": {
    "type": "eip1186",
    "accountProof": ["0x...", "0x..."],
    "storageProof": [],
    "blockHash": "0x..."
  }
}
```

For receipt proofs:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": { ... },
  "proof": {
    "type": "receipt",
    "receiptProof": ["0x...", "0x..."],
    "blockHash": "0x...",
    "receiptsRoot": "0x..."
  }
}
```

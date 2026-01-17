# RPC Methods

DVRPC supports standard Ethereum JSON-RPC methods. It's a drop-in replacement for any centralized RPC provider.

## How It Works

Every request is verified internally against a light client's consensus-verified state root.

By default, responses look identical to any other RPC provider. Add the `X-DVRPC-Proof` header to receive cryptographic proofs with your response.

## Proof Header

| Header | Value | Effect |
|--------|-------|--------|
| X-DVRPC-Proof | true | Include proof + consensus data in response |

## Response Formats

### Standard Response (no header)

```bash
curl -X POST https://rpc.dvrpc.org \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x742d35Cc6634C0532925a3b844Bc9e7595f5bE91","latest"],"id":1}'
```

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x1234"
}
```

### Verified Response (with header)

```bash
curl -X POST https://rpc.dvrpc.org \
  -H "Content-Type: application/json" \
  -H "X-DVRPC-Proof: true" \
  -d '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x742d35Cc6634C0532925a3b844Bc9e7595f5bE91","latest"],"id":1}'
```

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x1234",
  "proof": {
    "accountProof": [
      "0xf90211a0...",
      "0xf90211a0...",
      "0xf8719d..."
    ],
    "storageProof": []
  },
  "consensus": {
    "stateRoot": "0xabc123...",
    "slot": 1234567,
    "syncCommitteeBits": "0xffff...",
    "syncCommitteeSignature": "0x8234..."
  }
}
```

## What the Proof Contains

| Field | Description |
|-------|-------------|
| proof.accountProof | Merkle proof path from state root to account |
| proof.storageProof | Merkle proofs for requested storage slots |
| consensus.stateRoot | State root verified by light client |
| consensus.slot | Beacon chain slot number |
| consensus.syncCommitteeBits | Which sync committee members signed |
| consensus.syncCommitteeSignature | BLS signature from sync committee |

## How to Verify (Client-Side)

1. Verify sync committee signature is valid for the slot
2. Confirm state root is signed by sync committee
3. Verify account proof against state root
4. Data is now trustlessly verified

We will provide a JS library for client-side verification.

## Supported Methods

### Phase 1

| Method | Proof Type | Description |
|--------|------------|-------------|
| eth_getBalance | Account proof | Get account balance |
| eth_getStorageAt | Account + storage proof | Get storage value |
| eth_getTransactionCount | Account proof | Get account nonce |
| eth_getCode | Account proof | Get contract code |
| eth_getProof | Full proof | Direct proof access (EIP-1186) |

### Phase 2

| Method | Proof Type | Description |
|--------|------------|-------------|
| eth_getTransactionReceipt | Receipt proof | Get transaction receipt |
| eth_getLogs | Receipt proofs | Get event logs |

### Pass-through Methods

These methods are passed to upstream RPC without verification:

- eth_blockNumber
- eth_chainId
- eth_gasPrice
- eth_call (verification planned)
- eth_estimateGas
- eth_sendRawTransaction

## Why This Matters

**Without DVRPC:**
```
Client → RPC Provider → "Balance is 5 ETH" (trust provider)
```

**With DVRPC (no header):**
```
Client → DVRPC → verifies internally → "Balance is 5 ETH" (trust DVRPC verified it)
```

**With DVRPC (with header):**
```
Client → DVRPC → "Balance is 5 ETH" + proof + consensus
Client verifies proof against consensus-signed state root
Trust nothing, verify everything
```

## Integration Examples

### JavaScript (ethers.js)

```javascript
// Standard usage - drop-in replacement
const provider = new ethers.JsonRpcProvider('https://rpc.dvrpc.org');
const balance = await provider.getBalance(address);

// With proof - custom fetch
const response = await fetch('https://rpc.dvrpc.org', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-DVRPC-Proof': 'true'
  },
  body: JSON.stringify({
    jsonrpc: '2.0',
    method: 'eth_getBalance',
    params: [address, 'latest'],
    id: 1
  })
});

const { result, proof, consensus } = await response.json();
// Verify proof client-side...
```

### Python (web3.py)

```python
from web3 import Web3

# Standard usage - drop-in replacement
w3 = Web3(Web3.HTTPProvider('https://rpc.dvrpc.org'))
balance = w3.eth.get_balance(address)

# With proof - custom request
import requests

response = requests.post(
    'https://rpc.dvrpc.org',
    headers={
        'Content-Type': 'application/json',
        'X-DVRPC-Proof': 'true'
    },
    json={
        'jsonrpc': '2.0',
        'method': 'eth_getBalance',
        'params': [address, 'latest'],
        'id': 1
    }
)

data = response.json()
# data['result'], data['proof'], data['consensus']
```

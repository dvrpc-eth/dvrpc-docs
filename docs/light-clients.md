# Light Clients

## Role in DVRPC

The light client provides **verified state roots**.

DVRPC doesn't implement consensus. It integrates existing light client implementations that already solve this problem. The light client syncs with the beacon chain and provides verified block headers, which DVRPC uses to verify proofs.

## Pluggable Architecture

DVRPC is designed to work with any Ethereum light client that provides:

- **Verified state roots** - The state root for a given block
- **Header tracking** - Access to verified block headers
- **Finality information** - Whether a block is finalized/safe/head

## Why Pluggable?

- **No single point of failure** - Bugs in one light client won't affect all DVRPC nodes
- **Extensible** - Anyone can add support for new light clients
- **Operator choice** - Node operators choose what fits their setup

## Integration Interface

Light clients will need to implement the `ConsensusProvider` trait:

```rust
trait ConsensusProvider {
    /// Get verified state root for a block
    fn get_state_root(&self, block: BlockId) -> Result<H256>;

    /// Get verified block header
    fn get_header(&self, block: BlockId) -> Result<Header>;

    /// Get finality status (finalized/safe/head)
    fn get_finality_status(&self, block: BlockId) -> Result<FinalityStatus>;
}
```

This minimal interface will allow integration with any light client implementation, whether as a library or sidecar process.

## What the Light Client Does NOT Do

- **Not involved in P2P** - Each node's light client syncs independently
- **Not shared across nodes** - No header gossip between DVRPC nodes
- **Not our code** - We integrate existing implementations, not build our own

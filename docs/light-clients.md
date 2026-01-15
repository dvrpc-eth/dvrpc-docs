# Light Clients

## Pluggable Architecture

DVRPC is designed to work with any Ethereum light client that provides:

- Verified state roots
- Header tracking
- Finality information

## Planned Light Client Support

| Client | Language | Integration | Status |
|--------|----------|-------------|--------|
| Helios | Rust | Library | First target |
| Lodestar | TypeScript | Sidecar | Planned |
| Nimbus | Nim | Sidecar | Planned |

## Why Pluggable?

- **No single point of failure** - Bugs in one light client won't affect all DVRPC nodes
- **Community extensible** - Anyone will be able to add support for new light clients
- **Operator choice** - Node operators will choose what fits their setup and trust model

## Adding a Light Client

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

This minimal interface will allow integration with any light client implementation.

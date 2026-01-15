# DVRPC

**Decentralized Verified RPC node for Ethereum**

DVRPC serves JSON-RPC with cryptographic proofs. It integrates existing light clients for consensus verification, adds proof generation and a P2P layer on top. Pluggable architecture - not tied to any single light client implementation.

## Why DVRPC?

Most Ethereum applications blindly trust centralized RPC providers. DVRPC provides a drop-in replacement that cryptographically verifies every response.

## Documentation

- [Introduction](docs/introduction.md) - Problem statement and approach
- [Architecture](docs/architecture.md) - System components and data flow
- [Getting Started](docs/getting-started.md) - Installation and usage
- [RPC Methods](docs/rpc-methods.md) - Supported JSON-RPC methods
- [Verification](docs/verification.md) - How proofs work
- [Light Clients](docs/light-clients.md) - Pluggable light client integrations
- [Roadmap](docs/roadmap.md) - Development phases

## Quick Start

Coming soon.

## License

MIT

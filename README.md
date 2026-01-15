# DVRPC

**Decentralized Verified RPC node for Ethereum**

DVRPC will serve JSON-RPC with cryptographic proofs. It integrates existing light clients for consensus verification, adds proof generation and a P2P layer on top. Pluggable architecture - not tied to any single light client implementation.

> **Status:** Design phase. This documentation describes the planned architecture and functionality.

## Why DVRPC?

Most Ethereum applications blindly trust centralized RPC providers. DVRPC aims to provide a drop-in replacement that cryptographically verifies every response.

## Documentation

- [Introduction](docs/introduction.md) - Problem statement and approach
- [Architecture](docs/architecture.md) - Planned system components and data flow
- [Getting Started](docs/getting-started.md) - Installation and usage (coming soon)
- [RPC Methods](docs/rpc-methods.md) - Planned JSON-RPC methods
- [Verification](docs/verification.md) - How proofs will work
- [Light Clients](docs/light-clients.md) - Pluggable light client integrations
- [Roadmap](docs/roadmap.md) - Development phases

## License

MIT

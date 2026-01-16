# DVRPC

**Decentralized Verified RPC node for Ethereum**

> **Status:** Design phase. This documentation describes the planned architecture and functionality.

## What is DVRPC?

A hybrid system combining:

1. **Verification** - Light client verifies data, returns proofs to client
2. **Distributed Routing** - P2P network routes requests across nodes
3. **Open Access** - No token required, anyone can run or use

DVRPC integrates existing light clients (pluggable architecture) for consensus verification, adds proof generation, and distributes access via a P2P network.

## Documentation

- [Introduction](docs/introduction.md) - Problem statement and approach
- [Architecture](docs/architecture.md) - Verification and routing layers
- [Getting Started](docs/getting-started.md) - Planned installation and usage
- [RPC Methods](docs/rpc-methods.md) - Planned JSON-RPC methods
- [Verification](docs/verification.md) - How proofs work
- [Light Clients](docs/light-clients.md) - Pluggable light client integration
- [Roadmap](docs/roadmap.md) - Development phases

## License

MIT

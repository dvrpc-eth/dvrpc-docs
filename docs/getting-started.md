# Getting Started

> **Status:** Not yet available. This describes the planned usage.

## Planned Prerequisites

- Rust 1.75+
- Access to an Ethereum RPC endpoint (upstream provider)
- Access to a Beacon Chain endpoint

## Planned Installation

```bash
git clone https://github.com/user/dvrpc.git
cd dvrpc
cargo build --release
```

## Planned Configuration

`config.toml`:

```toml
[rpc]
host = "127.0.0.1"
port = 8545

[upstream]
url = "https://your-rpc-provider.com/YOUR_KEY"

[beacon]
url = "https://beacon-node.example.com"

[light_client]
type = "helios"
```

## Planned Usage

```bash
./target/release/dvrpc --config config.toml
```

Test with:

```bash
curl -X POST http://localhost:8545 \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

## Initial Limitations

First releases will have:

- Single upstream provider only
- No P2P networking
- Limited RPC method support

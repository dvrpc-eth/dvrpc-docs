# Architecture Diagrams

> **Note:** These diagrams represent the planned architecture.

## Two-Layer Architecture

```mermaid
flowchart TB
    subgraph Client
        APP[Application]
    end

    subgraph RoutingLayer["Routing Layer"]
        DHT[Kademlia DHT]
        ROUTE[Request Router]
        HEALTH[Health Tracker]
    end

    subgraph VerificationLayer["Verification Layer"]
        RPC[RPC Server]
        PE[Proof Engine]
        LC[Light Client]
        CACHE[Cache]
    end

    subgraph External
        UPSTREAM[Upstream RPC]
        BEACON[Beacon Chain]
    end

    APP -->|request| DHT
    DHT --> ROUTE
    ROUTE --> HEALTH
    HEALTH --> RPC
    RPC --> PE
    PE -->|eth_getProof| UPSTREAM
    LC -->|sync headers| BEACON
    PE -->|verify against| LC
    PE --> CACHE
    RPC -->|response + proof| APP
```

## State Proof Flow (EIP-1186)

```mermaid
sequenceDiagram
    participant Client
    participant DVRPC as DVRPC Node
    participant Upstream as Upstream RPC
    participant LC as Light Client

    Client->>DVRPC: eth_getBalance(addr)
    DVRPC->>Upstream: eth_getProof(addr)
    Upstream-->>DVRPC: account + proof
    DVRPC->>LC: get_state_root(block)
    LC-->>DVRPC: verified state root
    DVRPC->>DVRPC: verify proof against root
    DVRPC-->>Client: balance + proof
```

## Receipt Proof Flow (Our Implementation)

```mermaid
sequenceDiagram
    participant Client
    participant DVRPC as DVRPC Node
    participant Upstream as Upstream RPC
    participant LC as Light Client

    Client->>DVRPC: eth_getTransactionReceipt(txHash)
    DVRPC->>Upstream: eth_getBlockReceipts(block)
    Upstream-->>DVRPC: all receipts
    DVRPC->>DVRPC: build receipt trie
    DVRPC->>DVRPC: generate Merkle proof
    DVRPC->>LC: get_header(block)
    LC-->>DVRPC: header with receiptsRoot
    DVRPC->>DVRPC: verify against receiptsRoot
    DVRPC-->>Client: receipt + proof
```

## P2P Routing Layer

```mermaid
flowchart LR
    subgraph Clients
        C1[Client 1]
        C2[Client 2]
        C3[Client 3]
    end

    subgraph P2P["P2P Network"]
        N1[Node 1]
        N2[Node 2]
        N3[Node 3]
        N4[Node 4]
    end

    C1 --> N1
    C1 -.-> N2
    C2 --> N2
    C2 -.-> N3
    C3 --> N3
    C3 -.-> N4

    N1 <-->|DHT| N2
    N2 <-->|DHT| N3
    N3 <-->|DHT| N4
    N4 <-->|DHT| N1
```

## Light Client Integration (Pluggable)

```mermaid
flowchart TD
    subgraph Implementations["Light Client Implementations"]
        LC1[Light Client A]
        LC2[Light Client B]
        LC3[Light Client C]
    end

    subgraph DVRPC["DVRPC"]
        TRAIT[ConsensusProvider Trait]
        PE[Proof Engine]
    end

    LC1 -->|implements| TRAIT
    LC2 -->|implements| TRAIT
    LC3 -->|implements| TRAIT
    TRAIT --> PE
```

## What We Use vs What We Build

```mermaid
flowchart LR
    subgraph Existing["We Use (Existing)"]
        LC[Light Clients]
        EIP[eth_getProof]
        LIBP2P[libp2p]
    end

    subgraph Build["We Build"]
        INT[Integration Layer]
        VERIFY[Verification Logic]
        RECEIPT[Receipt Proofs]
        ROUTING[Discovery + Routing]
    end

    LC --> INT
    EIP --> VERIFY
    LIBP2P --> ROUTING
```

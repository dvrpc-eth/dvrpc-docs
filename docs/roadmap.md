# Roadmap

## Phase 1: Foundation

- [x] Project setup
- [ ] Light client integration layer
- [ ] Basic RPC server
- [ ] Upstream request forwarding

## Phase 2: State Proofs

- [ ] `eth_getProof` integration
- [ ] State proof verification against light client
- [ ] `eth_getBalance` with proofs
- [ ] `eth_getStorageAt` with proofs

## Phase 3: Receipt Proofs

- [ ] Receipt trie construction
- [ ] Receipt proof generation
- [ ] `eth_getTransactionReceipt` with proofs
- [ ] Verification against `receiptsRoot`

## Phase 4: Caching

- [ ] Proof caching layer
- [ ] Header caching
- [ ] Cache invalidation strategy

## Phase 5: P2P Discovery

- [ ] libp2p integration
- [ ] Kademlia DHT for node discovery
- [ ] Node health tracking
- [ ] Peer management

## Phase 6: P2P Routing

- [ ] Request routing across nodes
- [ ] Load balancing
- [ ] Failover handling

## Phase 7: Testnet

- [ ] Public testnet deployment
- [ ] Documentation for node operators
- [ ] Monitoring and metrics

## Phase 8: Extended Proofs

- [ ] Log proofs (`eth_getLogs`)
- [ ] Block proof batching
- [ ] Performance optimization

## Phase 9: Client Library

- [ ] JS verification library
- [ ] Client-side proof verification
- [ ] npm package release

## Phase 10: Production

- [ ] Security audit
- [ ] Mainnet deployment
- [ ] Public launch

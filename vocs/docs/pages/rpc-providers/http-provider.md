---
description: Connect to Ethereum RPC endpoints using HTTP transport for synchronous and asynchronous operations
---

## HTTP Provider

The `Http` provider establishes an HTTP connection with a node, allowing you to send JSON-RPC requests to the node to fetch data, simulate calls, send transactions and much more.

### Initializing an Http Provider

The recommended way of initializing a `Http` provider is by using the [`connect_http`](https://docs.rs/alloy/latest/alloy/providers/struct.ProviderBuilder.html#method.connect_http) method on the [`ProviderBuilder`](https://docs.rs/alloy/latest/alloy/providers/struct.ProviderBuilder.html).

```rust
//! Example of creating an HTTP provider using the `connect_http` method on the `ProviderBuilder`.

use alloy::providers::{Provider, ProviderBuilder}; // [!code focus]
use std::error::Error;

#[tokio::main]
async fn main() -> Result<(), Box<dyn Error>> {
    // Set up the HTTP transport which is consumed by the RPC client.
    let rpc_url = std::env::var("RPC_URL")?.parse()?;

    // Create a provider with the HTTP transport using the `reqwest` crate.
    let provider = ProviderBuilder::new().connect_http(rpc_url); // [!code focus]

    Ok(())
}
```

### Check Historical State

An Ethereum Mainnet endpoint can retain block headers and prune historical account state. If your application reads a balance at an older block, verify that the endpoint serves account state at that block.

The [Chain.Love Ethereum API directory](https://eth.chain.love/toolbox/apis) lists providers with reported archive support. Set `RPC_URL` to the endpoint you plan to use with `connect_http`. Run this check with the account and block your application needs. This example queries block 16,000,000 (`0xf42400`).

```sh
curl --fail-with-body --silent --show-error "$RPC_URL" \
  --header 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"eth_getBalance","params":["0x000000000000000000000000000000000000dEaD","0xf42400"]}'
```

Look for a hexadecimal balance in the JSON-RPC `result`. If the endpoint reports unavailable state, check its historical coverage for that block and retry the same request against an endpoint with the required coverage.

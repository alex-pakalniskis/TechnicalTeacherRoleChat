## Subgraph queries

``` gql
query CurrentEpoch {
  epoches(orderBy: startBlock, orderDirection: desc, first: 1) {
    id
  }
}
```

``` gql
query OldAllocations {
  allocations(where:{createdAtEpoch_lt:<replace value with Current Epoch - 28>}) {
    id
    createdAtEpoch
    allocatedTokens
    indexer {
      id
    }
  }
}
```

## Rust stuff
### Create new `cargo` project

``` bash
cargo new dec2022_rolechat
cd dec2022_rolechat
```

### Update `Cargo.toml` dependencies

``` toml
[dependencies]
serde = { version = "1.0.149", features = ["derive"] }
reqwest = { version = "0.11", features =  ["json"]}
tokio = { version = "1.23.0", features = ["full"] }
```

### Update `src/main.rs`

``` rust
use std::collections::HashMap;
use serde::Deserialize;
use std::string::String;

#[derive(Debug, Deserialize, PartialEq)]
struct Epoch {
    id: String,
}

#[derive(Debug, Deserialize, PartialEq)]
struct EpochData {
    epoches: Vec<Epoch>,
}

#[derive(Debug, Deserialize, PartialEq)]
struct EpochResponse {
    data: EpochData,
}

#[derive(Debug, Deserialize, PartialEq)]
struct Indexer {
    id: String,
}

#[derive(Debug, Deserialize, PartialEq)]
struct Allocation {
    id: String,
    createdAtEpoch: i32,
    allocatedTokens: String,
    indexer: Indexer,
}

#[derive(Debug, Deserialize, PartialEq)]
struct AllocationsData {
    allocations: Vec<Allocation>,
}

#[derive(Debug, Deserialize, PartialEq)]
struct AllocationsResponse {
    data: AllocationsData,
}


#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let epoch_subgraph_query = "{epoches(orderBy: startBlock, orderDirection: desc, first: 1) {id}}";
    let subgraph_url = "https://api.thegraph.com/subgraphs/name/graphprotocol/graph-network-mainnet";
    
    let mut map = HashMap::new();
    map.insert("query", epoch_subgraph_query);

    let epoch_res: EpochResponse = reqwest::Client::new()
    .post(subgraph_url)
    .json(&map)
    .send()
    .await?
    .json()
    .await?;

    let current_epoch = epoch_res.data.epoches[0].id.parse::<i32>().unwrap();
    let oldest_allowable_epoch = current_epoch - 28;
    
    let allocations_subgraph_query = format!("{{ allocations(where:{{ createdAtEpoch_lt:{} }}) {{ id createdAtEpoch allocatedTokens indexer {{ id }} }} }}", oldest_allowable_epoch);
    
    let mut map = HashMap::new();
    map.insert("query", allocations_subgraph_query);

    let allocations_res: AllocationsResponse = reqwest::Client::new()
    .post(subgraph_url)
    .json(&map)
    .send()
    .await?
    .json()
    .await?;

    for allo in allocations_res.data.allocations {
        println!("{}: {} epoches old", allo.id, current_epoch - allo.createdAtEpoch );
    }

    Ok(())
}
```

### Build and run the code

```bash
cargo run
```

## Subgraph query

``` gql
query CurrentEpoch {
  epoches(orderBy: startBlock, orderDirection: desc, first: 1) {
    id
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
struct Data {
    epoches: Vec<Epoch>,
}

#[derive(Debug, Deserialize, PartialEq)]
struct Response {
    data: Data,
}

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let subgraph_query = "{epoches(orderBy: startBlock, orderDirection: desc, first: 1) {id}}";
    let subgraph_url = "https://api.thegraph.com/subgraphs/name/graphprotocol/graph-network-mainnet";
    
    let mut map = HashMap::new();
    map.insert("query", subgraph_query);

    let res: Response = reqwest::Client::new()
    .post(subgraph_url)
    .json(&map)
    .send()
    .await?
    .json()
    .await?;

    println!("The current epoch is {}", res.data.epoches[0].id);

    Ok(())
}
```

### Build and run the code

```bash
cargo run
```

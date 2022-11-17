# TechnicalTeacherRoleChat
## Getting Started
1. Open a web browser
1. Navigate to [`graph-network-mainnet` subgraph explorer](https://api.thegraph.com/subgraphs/name/graphprotocol/graph-network-mainnet/graphql?query=) 
1. Start querying

## Example Queries
### Current Epoch

``` gql
query CurrentEpoch {
  epoches(orderBy: startBlock, orderDirection: desc, first: 1) {
    id
  }
}
```

### Old Open Allocations
The Graph docs:https://thegraph.com/docs/en/network/indexing/#the-life-of-an-allocation

* `maxAllocationEpochs` = 28

Very old allocations threshold: CurrentEpoch - ( 5 * maxAllocationEpochs)


```gql
query OpenAllocationsOld {
  allocations(where:,{createdAtEpoch_lt:543}) {
    id,
    createdAtEpoch
    allocatedTokens
    poi
    indexer {
      id
      defaultDisplayName
    }
  }
}
```

## Automate dynamic query with some Python scripting

todo




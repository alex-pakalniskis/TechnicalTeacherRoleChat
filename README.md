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

1. Please open a new Python Jupyter Notebook in your Google Drive: https://colab.research.google.com/#create=true
1. In a new code cell, kindly import the libraries we'll be using today
    ``` python
    import requests
    from string import Template
    ```
    
1. In a new code cell, please define two functions to recreate our GraphQL subgraph queries
    ``` python
    def get_current_epoch():
      query = Template("""
      {
        epoches(orderBy: startBlock, orderDirection: desc, first: 1) {
          id
        }
      }
      """)

      return query.safe_substitute()
    
    def get_old_allocations(oldest_allowable_epoch: int):
      query = Template("""
      {
        allocations(where: {createdAtEpoch_lt:$oldest_epoch},) {
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
      """)
      
      return query.safe_substitute(
        oldest_epoch=oldest_allowable_epoch
        )
    ```

1. 


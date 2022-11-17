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

1. Open terminal
1. Create a new directory and change into it
    ``` bash
    mkdir tech_teach_role_chat && cd tech_teach_role_chat
    ```
1. Create some files for our mini app
    ``` bash
    touch query.py utils.py app.py 
    ```
1. Open VSCode from within the folder
    ``` bash
    code .
    ```
1. Update `query.py` with the following code
    * We're converting our GraphQL queries into Python string Templates
    * This will help us make dynamic GraphQL queries
    ``` python
    from string import Template
    
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




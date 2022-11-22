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
  allocations(where:{createdAtEpoch_lt:543}) {
    id
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
    import pandas as pd
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
          id
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

1. In the same code cell as the other functions, kindly define a third function that submits a generic GraphQL query to the `graph-network-mainnet` hosted subgraph.
    * We'll be able to pass either of the previous queries we've already defined as input
    * You could further parameterize this function to take a generic hosted subgraph as input
    ``` python
    def get_data(query):
      response = requests.post('https://api.thegraph.com/subgraphs/name/graphprotocol/graph-network-mainnet'
                               '',
                               json={
                                  "query":query
                                  })

      if response.status_code == 200: 
          return response.json()["data"]
      else:
          raise Exception(f"Query failed with return code {response.status_code}")
    ```

1. In the same code cell, please define one last function that automates our oldest allowable epoch logic
    * CurrentEpoch - ( 5 * maxAllocationEpochs)
    ``` python
    def oldest_allowable_epoch(current_epoch, max_allocation_epochs):
      return current_epoch - (5*max_allocation_epochs) 
    ```


1. Finally, in a new code cell please run all the functions to get our result
    ``` python
    max_allocation_epochs = 28
    current_epoch = int(get_data(get_current_epoch())["epoches"][0]["id"])
    oldest_epoch = oldest_allowable_epoch(current_epoch, max_allocation_epochs)
    old_allocations = get_data(get_old_allocations(oldest_epoch))["allocations"]
    old_allocations
    ```

1. Let's convert the Python dictionary into a pandas DataFrame (basically a spreadsheet) so it's easier to view the data
    ``` python
    df = pd.DataFrame.from_dict(old_allocations)
    df
    ```

1. Finally, let's do a bit of data cleaning to extract indexer IDs and defaultDisplayNames
    ``` python
    df["indexer_id"] = df["indexer"].apply(pd.Series)["id"]
    df["indexer_defaultDisplayName"] = df["indexer"].apply(pd.Series)["defaultDisplayName"]
    df.drop(columns=["indexer"], inplace=True)
    df
    ```

## Next Steps
* Implement pagination to return more than 100 results
* Connect results to a simple front end to display data in a more user friendly way
* The sky is the limit.

Have fun and query away!


## Resources
* [Reference Jupyter Notebook](https://colab.research.google.com/drive/19PNQ0f9qHgV_dwTTndhMCZeqhwsyhc7e?usp=sharing)

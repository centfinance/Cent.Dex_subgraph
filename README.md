<div align="center">
  <img alt="ReDoc logo" src="https://raw.githubusercontent.com/centfinance/Community/main/media-pack/logo.png" width="400px" />

</div>

<h1 align=center><code>Cent Dex Subgraph</code></h1>

The graphql schema is still under heavy development and will likely have major breaking changes.

Only the factory address is needed in subgraph.yaml, new pool addresses are automatically picked up using Graph Protocol's data source templates.


## Setup

### Prerequisites

- Global Yarn Packages
    - ganache-cli
    - truffle
    - graph-cli
- Docker

### Services

Start a ganache chain using 0.0.0.0 as a host so docker can connect

```
ganache-cli -h 0.0.0.0 -d -l 4294967295 --allowUnlimitedContractSize
```

Run a local graph node

```
git clone https://github.com/graphprotocol/graph-node/
```

Update ethereum value in docker-compose.yml to `ganache:http://host.docker.internal:8545`

```
cd graph-node/docker
```

```
docker-compose up
```

To blow away graph-node settings

```
docker-compose kill && docker-compose rm -f && rm -rf data
```

### Contracts

Deploy CentSwap contracts using truffle. Using the `yarn deploy` script in centswap-dapp also makes this easy to test out the subgraph using the frontend.

### Subgraph

Clone the Cent subgraph

```
git clone git@github.com:centfinance/Cent.ETH.Dex_subgraph.git
```

Update factory address in subgraph.yaml to the one listed as part of the deploy

Install dependencies

```
yarn
```

Generate the graph code

```
yarn codegen
```

Create local node

```
yarn create:local
```

Deploy locally

```
yarn deploy:local
```

Any updates can be made to this repo and re-running `yarn deploy:local` without needing to re-initialize the environment.

## Running Locally With Parity Kovan Node

Start Parity:

```
parity --chain=kovan --jsonrpc-interface=0.0.0.0
```

Update ethereum value in docker-compose.yml to `kovan:http://host.docker.internal:8545`

Comment out try_ functions in pool.ts LN52-64

```
cd graph-node/docker
```

```
docker-compose up
```

Create local node

```
yarn create:local
```

Deploy locally

```
yarn deploy:local
```

To blow away graph-node settings

```
docker-compose kill && docker-compose rm -f && rm -rf data
```


## Queries

GraphiQL interface can be accessed on a dev env at: http://127.0.0.1:8000/subgraphs/name/centfinance/cent-subgraph

**List of pools**
```GraphQL
{
  pools {
    id
    controller
    publicSwap
    finalized
    swapFee
    totalWeight
    totalShares
    createTime
    joinsCount
    exitsCount
    swapsCount
    tokens {
      id
      poolId {
        id
      }
      address
      balance
      denormWeight
    }
    shares {
      id
      poolId {
        id
      }
      userAddress {
        id
      }
    }
  }
}
```

**Pools with 2 tokens**
```GraphQL
{
  pools (where: {tokensList_contains: ["0x5b1869d9a4c187f2eaa108f3062412ecf0526b24", "0xcfeb869f69431e42cdb54a4f4f105c19c080a601"]}) {
    id
    publicSwap
    swapFee
    tokensList
    tokens {
      id
      address
      balance
      denormWeight
    }
  }
}
```

# Liquidation data from price simulation in Aave-V3

# Getting Started

## Requirements

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  - You'll know you did it right if you can run `git --version` and you see a response like `git version x.x.x`
- [Nodejs](https://nodejs.org/en/)
  - You'll know you've installed nodejs right if you can run:
    - `node --version` and get an ouput like: `vx.x.x`
- [Yarn](https://yarnpkg.com/getting-started/install) instead of `npm`
  - You'll know you've installed yarn right if you can run:
    - `yarn --version` and get an output like: `x.x.x`
    - You might need to [install it with `npm`](https://classic.yarnpkg.com/lang/en/docs/install/) or `corepack`

## Quickstart

```
git clone https://github.com/Tri-pathi/aave-mannet-fork.git
cd aave-mainnet-fork
yarn
```

# Fork Mainnet

Setup environment variables

You'll have to set your `MAINNET_RPC_URL` as environment variables. You can add them to a `.env` file.

- `MAINNET_RPC_URL`: This is url of the mainnet node you're working with. You can get setup with one for free from [Alchemy](https://alchemy.com/?a=673c802981) or from [Infura]

## Scripts

After deploy to a testnet or local net, you can run the scripts.

```
yarn hardhat run scripts/fetchusers.js
```

## Logic Walkthrough

### Find out all the users who can be liquidated if price of WBTC goes WBTC/2 in AAVE-V3

1. Fork the mainnet

2. Find the deployed PoolAddressProvider and get the updated live POOL contract and Oracle contract from PoolAddressProvider

3. To Simulate price change in aave contracts we need to update pricefeed of WBTC which gives the updated price i.e WBTC/2

   To change priceFeed in AAVE- AaveOracle contract, Signer should have owner role so impersonate the admin role and update the priceFeed of WBTC asset. Updated priceFeed should have interface of ICLSynchronicityPriceAdapter [check here](https://etherscan.io/address/0x230E0321Cf38F09e247e50Afc7801EA2351fe56F#code) and IAggregatorInterface ( of chainlink)

   for this I deployed a new priceFeed and update newPriceFeed into [assetSources](https://github.com/aave/aave-v3-core/blob/master/contracts/misc/AaveOracle.sol#L23) of AaveOracle

4. As of now simlulation has been completed . Find the users who has borrowd funds and get the current user information from [getUserAccountData](https://github.com/aave/aave-v3-core/blob/master/contracts/protocol/pool/Pool.sol) of Pool contract

5. Finding users who have borrowed funds can be tricky
   I have used graphQL to get the such users on quering [AAVE-V3-SUBGRAPH]("https://api.thegraph.com/subgraphs/name/aave/protocol-v3").

   I hope you can write better query than me :: 😊

   But GraphQl is slow not perfect for bots

   To make this faster indexing from Contract itseld could be better solution and perfect for bots

6. Push into the corresponding database depending upon the user's health Factor

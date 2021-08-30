# 如何将PancakeSwap部署到以太坊类链上

这个教程只包含部署了swap和liquidity功能
## 准备源码

下载`pancake-swap-core`源码

- Clone `pancake-swap-core`
```
git clone git@github.com:pancakeswap/pancake-swap-core.git
cd pancake-swap-core
git checkout -b factory 3b214306770e86bc3a64e67c2b5bdb566b4e94a7
yarn install
yarn compile
```

下载`pancake-swap-periphery`源码

- Clone `pancake-swap-periphery`
```
git clone git@github.com:pancakeswap/pancake-swap-periphery.git
cd pancake-swap-periphery
git checkout -b router d769a6d136b74fde82502ec2f9334acc1afc0732
yarn install
yarn add @uniswap/v2-core@"file:../pancake-swap-core"
yarn compile
```

下载`pancake-swap-interface-v1`前端源码


- Clone `pancake-swap-interface-v1`
```
git clone git@github.com:pancakeswap/pancake-swap-interface-v1.git
cd pancake-swap-interface-v1
git checkout -b v1 0257017f2daaae2f67c24ded70b5829f74a01b3c
yarn install
```


## 安装


### Install contract merger: https://www.npmjs.com/package/sol-merger

下载合约合并工具


```
npm install sol-merger -g
```


### Prepare `PancakeFactory` and `PancakeRouter` 

因为`PancakeFactory`和`PancakeRouter`的合约代码是好几个文件，我们把它们合并成一个方便部署，都放到build目录下

```
sol-merger pancake-swap-core/contracts/PancakeFactory.sol ./build
sol-merger pancake-swap-core/contracts/PancakePair.sol ./build
sol-merger pancake-swap-periphery/contracts/PancakeRouter01.sol ./build
sol-merger pancake-swap-periphery/contracts/PancakeRouter.sol ./build
```

### Prepare `WBNB.sol`and other tokens

准备WBNB.sol和其他咱们想在链上部署的token的合约代码，我都整理到了build/tokens目录下


+ New File: `WBNB.sol` => Copy source from https://bscscan.com/address/0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c#code

+ New File: `ETHToken.sol` => Copy source from https://bscscan.com/address/0x2170ed0880ac9a755fd29b2688956bd959f933f8#code

+ New File: `BUSDToken.sol` => Copy source from https://bscscan.com/address/0xe9e7cea3dedca5984780bafc599bd69add087d56#code

+ New File: `XRPToken.sol` => Copy source from https://bscscan.com/address/0x1d2f0da169ceb9fc7b3144628db156f3f6c60dbe#code

+ New File: `DAIToken.sol` => Copy source from https://bscscan.com/address/0x1af3f329e8be154074d8769d1ffa4ee058b1dbc3#code

+ New File: `CAKEToken.sol` => Copy source from https://bscscan.com/address/0x0e09fabb73bd3ade0a17ecc321fd13a19e81ce82#code

+ New File: `USDTToken.sol` => Copy source from https://bscscan.com/address/0x55d398326f99059ff775485246999027b3197955#code

+ New File: `BAKEToken.sol` => Copy source from https://bscscan.com/address/0xE02dF9e3e622DeBdD69fb838bB799E3F168902c5#code



#### Deploy Tokens

部署所有token 合约并记下合约地址


| token | address                                    | hash                                                         |
| ----- | ------------------------------------------ | ------------------------------------------------------------ |
| BAKE  | 0x94a42146Fab15a7EC00a8Bd95720cF0baDa65B42 | 0x6098e13955cf0853eb479f802bb4801f892258d8743f48178bd3b49e304d6bc9 |
| BUSD  | 0x7560AdfEca32ed5EC1b9101dD458e867Aec90cE3 | 0xfba1330074958956f9cff9112f51bb4589df011f3ebac3a9e891627f6f1a7811 |
| ETH   | 0xc34ad7105eb3B5F6c7A0198A346E26595A142291 | 0x0ceefc7c3d22dc3bc030ad33e62e402315a8aaae57968595d4d57b046ee5160e |
| USDT  | 0x02e996E902F5Ad8cC5037ECC5DCd552C565B6EBC | 0x95ed10809c17de41f2872708dcf646e43b4720c38293de57674454e893009db8 |
| XRP   | 0x0eF680193492C1cc0a34a20411bA31eeA3298eeA | 0x45324f79100d845ae410d666ec87cbda3a2b2650dcb3f5dae7d0ca82a99a3cbf |
| DAI   | 0x9D575ba54c57763d3b1e42957eBFE9D791DeeD39 | 0x5fda7bdadfe57e8b0334b99c7d699a5d2afdf8a2c12c2ee87ac5432cb69c8e7c |
| CAKE  | 0x4B13a328AFcc7EA26FA97436fB8a7346e6d2A6C6 | 0xc1c54f99aea29f3845c77b7a3f9b6ebfbe9aabb29a8414bacd0d879ddbfa27ed |



### Deploy `PancakeFactory` and `PancakeRouter`

- Access: https://remix.ethereum.org/#optimize=false&runs=200&evmVersion=null&version=soljson-v0.5.16+commit.9c3226ce.js

#### Deploy WBNB

+ New File: `WBNB.sol` => Copy source from https://bscscan.com/address/0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c#code
+ Compiler tab => Select compiler: `v0.8.3+commit.8d00100c`
+ Deploy tab => Select `WBNB` -> Deploy

#### Deploy MultiCall

+ New File: `MultiCall.sol` => Copy source from https://bscscan.com/address/0x1Ee38d535d541c55C9dae27B12edf090C608E6Fb#code
+ Compiler tab => Select compiler: `v0.8.3+commit.8d00100c`
+ Deploy tab => Select `MultiCall` -> Deploy

#### Deploy PancakeFactory

+ New File: `PancakeFactory.sol` => Copy source from `./build/PancakeFactory.sol`
+ Compiler tab => Select compiler: `v0.5.16+commit.9c3226ce`
+ Deploy tab => Select `PancakeFactory` -> Fill your address as `feeToSetter` in constructor -> Deploy

#### Deploy PancakeRouter01

+ New File: `PancakeRouter01.sol` => Copy source from `./build/PancakeRouter01.sol`
+ Expand `PancakeFactory` deployed above -> Read `INIT_CODE_PAIR_HASH` -> Copy this hash without prefix `0x`. Ex: `7f9a49918bf8ca6d4561dbce1f8d23f736f37c378e8a942d15ea4adf40d8ca6c`
+ Edit `PancakeRouter01`: Find `PancakeLibrary` -> `pairFor` function => Replace new hex by `INIT_CODE_PAIR_HASH` above. Ex: `hex'd0d4c4cd0848c93cb4fd1f498d7013ee6bfb25783ea21593d5834f5d250ece66'` -> `hex'7f9a49918bf8ca6d4561dbce1f8d23f736f37c378e8a942d15ea4adf40d8ca6c'`
+ Compiler tab => Select compiler: `v0.6.6+commit.6c089d02`
+ Deploy tab => Select `PancakeRouter01` -> Fill `PancakeFactory` address and `WBNB` address as constructor params -> Deploy

#### Deploy PancakeRouter (Main Router)

+ New File: `PancakeRouter.sol` => Copy source from `./build/PancakeRouter.sol`
+ Expand `PancakeFactory` deployed above -> Read `INIT_CODE_PAIR_HASH` -> Copy this hash without prefix `0x`. Ex: `7f9a49918bf8ca6d4561dbce1f8d23f736f37c378e8a942d15ea4adf40d8ca6c`
+ Edit `PancakeRouter`: Find `PancakeLibrary` -> `pairFor` function => Replace new hex by `INIT_CODE_PAIR_HASH` above. Ex: `hex'd0d4c4cd0848c93cb4fd1f498d7013ee6bfb25783ea21593d5834f5d250ece66'` -> `hex'7f9a49918bf8ca6d4561dbce1f8d23f736f37c378e8a942d15ea4adf40d8ca6c'`
+ Compiler tab => Select compiler: `v0.6.6+commit.6c089d02`; Check on `Enable optimization: 200` to avoid `Contract code size limit` issue
+ Deploy tab => Select `PancakeRouter` -> Fill `PancakeFactory` address and `WBNB` address as constructor params -> Deploy


#### Setup Frontend

安装前端代码

- Update .env.development 

修改配置文件

+ 修改`REACT_APP_NETWORK_URL` 为网络的RPC的URL

+ 修改`REACT_APP_CHAIN_ID` 为网络的chainID

```
cd pancake-swap-interface-v1
vi .env.development 


REACT_APP_NETWORK_URL="http://114.242.26.15:8006"
REACT_APP_CHAIN_ID="269"
REACT_APP_GTAG = "GTM-PXLD3XW"

```

更新部署的`PancakeRouter`地址

- Update `PancakeRouter` address to `ROUTER_ADDRESS` at `src/constants/index.ts`

更新所有链的`chainId`，我这里是`chainId`是269

- Update support chain to testnet at `src/connectors/index.ts`
	+ Change from `supportedChainIds: [56, 97]` to `supportedChainIds: [269]`
	+ Change from `56` to `269`

- Update support chaindId `97`to`269` at `src/connectors/index.ts`,
`src/utils/index.ts`


更新部署的`PancakeFactory`地址 和 `init code hash`

- Update `PancakeFactory` address and code hash to `FACTORY_ADDRESS` and `INIT_CODE_HASH` at `node_modules/@pancakeswap-libs/sdk/dist/constants.d.ts`, `node_modules/@pancakeswap-libs/sdk/dist/sdk.cjs.development.js`, `node_modules/@pancakeswap-libs/sdk/dist/sdk.cjs.production.min.js` and `node_modules/@pancakeswap-libs/sdk/dist/sdk.esm.js`

- Update `PancakeFactory` address to `v2 factory`; `PancakeRouter01` address to `v2 router 01` and `PancakeRouter` address to `v2 router 02` at `src/state/swap/hooks.ts`


更新所有`WBNB`地址

- Update `WBNB` address at `node_modules/@pancakeswap-libs/sdk/dist/sdk.cjs.development.js`, `node_modules/@pancakeswap-libs/sdk/dist/sdk.cjs.production.min.js`, `node_modules/@pancakeswap-libs/sdk/dist/sdk.esm.js`


更新所有 `MULTICALL_NETWORKS` 地址，修改测试网的地址

- Update `MULTICALL_NETWORKS` address `pancake-swap-interface-v1/src/constants/multicall/index.ts`

更新所有`token`的地址在`src/constants/index.ts`，并且除了这里
```js
const WETH_ONLY: ChainTokenList = {
  [ChainId.MAINNET]: [WETH[ChainId.MAINNET]],
  [ChainId.BSCTESTNET]: [WETH[ChainId.BSCTESTNET]],
}

```
把`ChainId.MAINNET`换成`ChainId.BSCTESTNET`


```js

export const CAKE = new Token(ChainId.BSCTESTNET, '0x4B13a328AFcc7EA26FA97436fB8a7346e6d2A6C6', 18, 'CAKE', 'PancakeSwap Token')
export const WBNB = new Token(ChainId.BSCTESTNET, '0x3180356fa8082efEEf9523BE654c162242E4dcC0', 18, 'WBNB', 'Wrapped BNB')
export const DAI = new Token(ChainId.BSCTESTNET, '0x9D575ba54c57763d3b1e42957eBFE9D791DeeD39', 18, 'DAI', 'Dai Stablecoin')
export const BUSD = new Token(ChainId.BSCTESTNET, '0x7560AdfEca32ed5EC1b9101dD458e867Aec90cE3', 18, 'BUSD', 'Binance USD')
export const USDT = new Token(ChainId.BSCTESTNET, '0x02e996E902F5Ad8cC5037ECC5DCd552C565B6EBC', 18, 'USDT', 'Tether USD')
export const ETH = new Token(ChainId.BSCTESTNET, '0xc34ad7105eb3B5F6c7A0198A346E26595A142291', 18, 'ETH', 'Binance-Peg Ethereum Token')
export const BAKE = new Token(ChainId.BSCTESTNET, '0x94a42146Fab15a7EC00a8Bd95720cF0baDa65B42', 18, 'BAKE', 'BAKE Token')

```

更新和 `pancake-swap-interface-v1/src/constants/token/pancakeswap.json` 将部署的`token`的`address`、`chainId`替换掉，`logoURI`可以替换成可以显示logo路径。

检查所有需要替换的都替换了

- VERIFY CHANGES by `Find All` old addresses and replace new ones:
	+ WBNB:            0xaE8E19eFB41e7b96815649A6a60785e1fbA84C1e
	+ PancakeFactory:  0xBCfCcbde45cE874adCB698cC183deBcF17952812
	+ INIT_CODE_HASH:  0xd0d4c4cd0848c93cb4fd1f498d7013ee6bfb25783ea21593d5834f5d250ece66
	+ PancakeRouter01: 0xf164fC0Ec4E93095b804a4795bBe1e041497b92a
	+ PancakeRouter:   0x05fF2B0DB69458A0750badebc4f9e13aDd608C7F
  + MULTICALL_NETWORKS:  0x301907b5835a2d723Fe3e9E8C5Bc5375d5c1236A
  
  

修改配置只显示swap和liquidity功能
- Custom menu at `src/components/Menu/config.ts`

```js

import { MenuEntry } from '@pancakeswap-libs/uikit'

const config: MenuEntry[] = [
  {
    label: 'Home',
    icon: 'HomeIcon',
    href: '/',
  },
  {
    label: 'Trade',
    icon: 'TradeIcon',
    initialOpenState: true,
    items: [
      {
        label: 'Exchange',
        href: '/swap',
      },
      {
        label: 'Liquidity',
        href: '/pool',
      },
    ],
  },
]

export default config


```


#### 添加自己token

- Deploy your own tokens
	+ Deploy your own tokens and update info (token address + chainId to 97) to `src/constants/token/pancakeswap.json`
	+ Remember update token icon with name as token address in lowercase mode to `public/images/coins`
	+ Update support network from `ChainId.MAINNET` to `ChainId.BSCTESTNET` at `src/constants/index.ts`
	+ Update coin addresses to your at `src/constants/index.ts`
	+ Update `src/components/Menu/index.tsx`: From `priceData.data[CAKE.address].price` to `priceData.data[CAKE.address]?.price ?? 0`
	+ Update `src/hooks/useGetDocumentTitlePrice.ts`: From `priceData.data[CAKE.address].price` to `priceData.data[CAKE.address]?.price ?? 0`
	


### Start and Build Frontend

- Start
```
yarn start
```

- Build
```
yarn build
```

### Deployment

- WBNB:            0x3180356fa8082efEEf9523BE654c162242E4dcC0
- PancakeFactory:  0xA7B372D22BeC0178ec561a74b87a95DF244E92a6
- INIT_CODE_HASH:  0x7f9a49918bf8ca6d4561dbce1f8d23f736f37c378e8a942d15ea4adf40d8ca6c
- PancakeRouter01: 0x07F0a23068F9cEa5F8D16ECE4354Fa4A5b670E36
- PancakeRouter:   0x953EDBf175792dB1161110543Cc2f4181fD6Dab5
- Frontend:        http://localhost:3000
- MULTICALL_NETWORKS 0xE3c149fc3252f9BF09cfb56B33a06A4b274B5d92

**Tokens**

| token | address                                    | hash                                                         |
| ----- | ------------------------------------------ | ------------------------------------------------------------ |
| BAKE  | 0x94a42146Fab15a7EC00a8Bd95720cF0baDa65B42 | 0x6098e13955cf0853eb479f802bb4801f892258d8743f48178bd3b49e304d6bc9 |
| BUSD  | 0x7560AdfEca32ed5EC1b9101dD458e867Aec90cE3 | 0xfba1330074958956f9cff9112f51bb4589df011f3ebac3a9e891627f6f1a7811 |
| ETH   | 0xc34ad7105eb3B5F6c7A0198A346E26595A142291 | 0x0ceefc7c3d22dc3bc030ad33e62e402315a8aaae57968595d4d57b046ee5160e |
| USDT  | 0x02e996E902F5Ad8cC5037ECC5DCd552C565B6EBC | 0x95ed10809c17de41f2872708dcf646e43b4720c38293de57674454e893009db8 |
| XRP   | 0x0eF680193492C1cc0a34a20411bA31eeA3298eeA | 0x45324f79100d845ae410d666ec87cbda3a2b2650dcb3f5dae7d0ca82a99a3cbf |
| DAI   | 0x9D575ba54c57763d3b1e42957eBFE9D791DeeD39 | 0x5fda7bdadfe57e8b0334b99c7d699a5d2afdf8a2c12c2ee87ac5432cb69c8e7c |
| CAKE  | 0x4B13a328AFcc7EA26FA97436fB8a7346e6d2A6C6 | 0xc1c54f99aea29f3845c77b7a3f9b6ebfbe9aabb29a8414bacd0d879ddbfa27ed |
| WBNB  | 0x3180356fa8082efEEf9523BE654c162242E4dcC0 | 0x33c7311e0acd78c0709cbc8f2141d2d168667c7aae1e1b967a44f3a64e748b9b |

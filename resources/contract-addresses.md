---
description: Addresses and Etherscan links for deployed JB Protocol and JBX DAO contracts.
---

# Contract Addresses

## Juicebox Protocol V1

### Ethereum Mainnet

TerminalDirectory: [`0x46C9999A2EDCD5aA177ed7E8af90c68b7d75Ba46`](https://etherscan.io/address/0x46c9999a2edcd5aa177ed7e8af90c68b7d75ba46)

TerminalV1: [`0xd569D3CCE55b71a8a3f3C418c329A66e5f714431`](https://etherscan.io/address/0xd569D3CCE55b71a8a3f3C418c329A66e5f714431)

Projects: [`0x9b5a4053FfBB11cA9cd858AAEE43cc95ab435418`](https://etherscan.io/address/0x9b5a4053FfBB11cA9cd858AAEE43cc95ab435418)\`\`

TicketBooth: [`0xee2eBCcB7CDb34a8A822b589F9E8427C24351bfc`](https://etherscan.io/address/0xee2eBCcB7CDb34a8A822b589F9E8427C24351bfc)

ModStore: [`0xB9E4B658298C7A36BdF4C2832042A5D6700c3Ab8`](https://etherscan.io/address/0xB9E4B658298C7A36BdF4C2832042A5D6700c3Ab8)

OperatorStore: [`0xab47304D987390E27Ce3BC0fA4Fe31E3A98B0db2`](https://etherscan.io/address/0xab47304D987390E27Ce3BC0fA4Fe31E3A98B0db2)

FundingCycles: [`0xf507B2A1dD7439201eb07F11E1d62AfB29216e2E`](https://etherscan.io/address/0xf507B2A1dD7439201eb07F11E1d62AfB29216e2E)

Active7DaysFundingCycleBallot: [`0xEf7480b6E7CEd228fFB0854fe49A428F562a8982`](https://etherscan.io/address/0xEf7480b6E7CEd228fFB0854fe49A428F562a8982)

Active3DaysFundingCycleBallot: [`0x6d6da471703647Fd8b84FFB1A29e037686dBd8b2`](https://etherscan.io/address/0x6d6da471703647Fd8b84FFB1A29e037686dBd8b2)

Active1DayFundingCycleBallot: N/A

Governance: [`0xAc43e14c018490D045a774008648c701cda8C6b3`](https://etherscan.io/address/0xAc43e14c018490D045a774008648c701cda8C6b3)

Prices: [`0xa9537Cc42555564206D4E57c0eb6943d56E83A30`](https://etherscan.io/address/0xa9537Cc42555564206D4E57c0eb6943d56E83A30)\`\`

### Rinkeby

[https://github.com/jbx-protocol/juice-contracts/tree/main/deployments/rinkeby](https://github.com/jbx-protocol/juice-contracts/tree/main/deployments/rinkeby)

### Kovan

TerminalDirectory Kovan: [`0x71BA69044CbD951AC87124cBEdbC0334AB21F26D`](https://kovan.etherscan.io/address/0x71BA69044CbD951AC87124cBEdbC0334AB21F26D)

## Juicebox DAO

### $JBX

Mainnet ERC-20 Token: [0x3abf2a4f8452ccc2cf7b4c1e4663147600646f66](https://etherscan.io/token/0x3abf2a4f8452ccc2cf7b4c1e4663147600646f66)

{% hint style="info" %}
To reduce gas fees, newly issued $JBX tokens are stored in the Juicebox [TicketBooth](../protocol-v1/ticketbooth/) contract by default ("staked"). $JBX holders can call the `unstake` function on the TicketBooth contract to mint $JBX ERC-20 tokens to their wallets. In the frontend, this is called `Claim` and can be found under the `Manage` button.

The above ERC-20 contract reflects the total supply of minted ERC-20 $JBX tokens.

To ascertain the total supply of claimed (ERC-20) and unclaimed ("staked") $JBX tokens, call the `totalSupplyOf()` function on the TicketBooth contract above, passing project id `1` as the argument.
{% endhint %}

### Gnosis Multisig

#### Mainnet

Etherscan: [https://etherscan.io/address/0xaf28bcb48c40dbc86f52d459a6562f658fc94b1e](https://etherscan.io/address/0xaf28bcb48c40dbc86f52d459a6562f658fc94b1e)

Gnosis Safe: [https://gnosis-safe.io/app/eth:0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e/balances](https://gnosis-safe.io/app/eth:0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e/balances)

#### **Rinkeby**

Etherscan: [https://rinkeby.etherscan.io/address/0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e](https://rinkeby.etherscan.io/address/0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e)

Gnosis Safe: [https://gnosis-safe.io/app/rin:0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e/balances](https://gnosis-safe.io/app/rin:0xAF28bcB48C40dBC86f52D459A6562F658fc94B1e/balances)

## Juicebox Protocol V2

### Rinkeby

JBOperatorStore: [`0x55d4dfb578daA4d60380995ffF7a706471d7c719`](https://rinkeby.etherscan.io/address/0x55d4dfb578daA4d60380995ffF7a706471d7c719)

JBPrices: [`0x47C6072ccDb899C016ED07ae8aEb7b2cfFe3C82e`](https://rinkeby.etherscan.io/address/0x47C6072ccDb899C016ED07ae8aEb7b2cfFe3C82e)

JBProjects: [`0x2B0b6BD05a2F1f2a399F73528a99a495555C4c52`](https://rinkeby.etherscan.io/address/0x2B0b6BD05a2F1f2a399F73528a99a495555C4c52)

JBDirectory: [`0xF77Cc21F7Ffdb0700D6d01FCF32EBE654f1A389b`](https://rinkeby.etherscan.io/address/0xF77Cc21F7Ffdb0700D6d01FCF32EBE654f1A389b)

JBFundingCycleStore: [`0xF77Cc21F7Ffdb0700D6d01FCF32EBE654f1A389b`](https://rinkeby.etherscan.io/address/0xfd6Bc33C9e25c6d9Bbd00b04992E3639E786DCEd)

JBTokenStore: [`0x29431d36f382f50878399C1D529b20582573AAb6`](https://rinkeby.etherscan.io/address/0x29431d36f382f50878399C1D529b20582573AAb6)

JBSplitsStore: [`0xAa818525455C52061455a87C4Fb6F3a5E6f91090`](https://rinkeby.etherscan.io/address/0xAa818525455C52061455a87C4Fb6F3a5E6f91090)

JBController: [`0xd2eEEdB22f075eBFf0a2A7D38781AA320CBc357E`](https://rinkeby.etherscan.io/address/0xd2eEEdB22f075eBFf0a2A7D38781AA320CBc357E)

JBPaymentTerminalStore: [`0x92239434A7d0D17c4d8F876C7DB75E54680Bc85e`](https://rinkeby.etherscan.io/address/0x92239434A7d0D17c4d8F876C7DB75E54680Bc85e)

JBETHPaymentTerminal: [`0x9d5687A9A175308773Bb289159Aa61D326E3aDB5`](https://rinkeby.etherscan.io/address/0x9d5687A9A175308773Bb289159Aa61D326E3aDB5)

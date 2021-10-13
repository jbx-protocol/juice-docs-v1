---
description: >-
  Manages token minting and burning for all projects, while keeping track of
  unclaimed token balances for all accounts.
---

# JBTokenStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBTokenstore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

|                     |                                                                                                                                          |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**            | Description                                                                                                                              |
| **`IJBTokenStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

|                    |                                                                                                                                                                        |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**           | Description                                                                                                                                                            |
| **`JBOperatable`** | <p>Includes convenience functionality for checking a message sender's permissions before executing certain transactions.</p><p><a href="../jboperatable/">more</a></p> |
| **`JBControllerUtility`**    | Includes convenience functionality for checking if the message sender is the current controller of the project who data is being manipulated.                            |

## Events

| Name                        | Data                                                                                                                                                                                                                                                                                                                  |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Issue`**                 | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBToken indexed token</code></li><li><code>string name</code></li><li><code>string symbol</code></li><li><code>address caller</code></li></ul><p><a href="events/issue.md">more</a></p>                                                                 |
| **`Mint`**                  | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>bool tokensWereClaimed</code></li><li><code>bool preferClaimedTokens</code></li><li><code>address caller</code></li></ul><p><a href="events/mint.md">more</a></p>        |
| **`Burn`**                  | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>uint256 unclaimedTokenBalance</code></li><li><code>bool preferClaimedTokens</code></li><li><code>address caller</code></li></ul><p><a href="events/burn.md">more</a></p> |
| **`Claim`**                 | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul><p><a href="events/claim.md">more</a></p>                                                                                                 |
| **`ShouldRequireClaim`** | <ul><li><code>uint256 indexed projectId</code></li><li><code>bool indexed flag</code></li><li><code>address caller</code></li></ul><p><a href="events/shouldrequireclaim.md">more</a></p>                                                                                                                             |
| **`ChangeToken`**           | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBToken indexed token</code></li><li><code>address indexed owner</code></li><li><code>address caller</code></li></ul><p><a href="events/usenewtoken.md">more</a></p>                                                                                    |
| **`Transfer`**              | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed recipient</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul><p><a href="events/transfer.md">more</a></p>                                               |

## Read

| Function                     | Definition                                                                                                                                                                                                                                                            |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`projects`**               | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBProjects projects</code></li></ul><p><a href="properties/projects.md">more</a></p>                                                              |
| **`tokenOf`**                | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBToken token</code></li></ul><p><a href="properties/tokenof.md">more</a></p>                                                            |
| **`unclaimedBalanceOf`**     | <p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 unclaimedBalance</code></li></ul><p><a href="properties/unclaimedbalanceof.md">more</a></p>  |
| **`unclaimedTotalSupplyOf`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 unclaimedTotalSupply</code></li></ul><p><a href="properties/unclaimedtotalsupplyof.md">more</a></p>                               |
| **`totalSupplyOf`**          | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 totalSupply</code></li></ul><p><a href="read/totalsupplyof.md">more</a></p>                                                       |
| **`balanceOf`**              | <p><strong>Params</strong></p><ul><li><code>uint256 _holder</code></li><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 balance</code></li></ul><p><a href="../jbpaymentterminaldata/read/balanceof.md">more</a></p> |
| **`requireClaimFor`**        | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool flag</code></li></ul><p><a href="properties/requireclaimfor.md">more</a></p>                                                         |

## Write

| Function                       | Definition                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`issueFor`**                 | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _name</code></li><li><code>string _symbol</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBToken token</code></li></ul><p><a href="write/issuefor.md">more</a></p> |
| **`changeTokenOf`**            | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>IJBToken _token</code></li><li><code>address _newOwner</code></li></ul><p><a href="write/changetokenof.md">more</a></p>                                                                  |
| **`mintFor`**                  | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>bool _preferUnstakedTokens</code></li></ul><p><a href="write/mintfor.md">more</a></p>                               |
| **`burnFrom`**                 | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>bool _preferUnstakedTokens</code></li></ul><p><a href="write/burnfrom.md">more</a></p>                              |
| **`claimFor`**                 | <p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><a href="write/claimfor.md">more</a></p>                                                                                                                                                       |
| **`transferTo`**               | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _recipient</code></li><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><a href="write/transferto.md">more</a></p>                               |
| **`shouldRequireClaimingFor`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>bool _flag</code></li></ul><p><a href="write/shouldrequireclaimingfor.md">more</a></p>                                                                                                                                                                                 |

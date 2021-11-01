---
description: >-
  An ERC-20 token that can be minted and burned by its owner.
---

# JBTokenStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBToken.sol" %}

### **Interfaces**

| Name                                                     | Description                                                                                                                              |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBToken`**](../../../interfaces/ijbtoken.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |


### **Inheritance**

| Contract                                                         | Description                                                                                                                                                                        |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ERC20`**](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#ERC20) | General token standard for fungible accounting. |
| [**`ERC20Permit`**](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#ERC20Permit) | Allows pre-signing approvals for ERC-20 token transfers. |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/4.x/api/security) | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |

## Constructor

```solidity
constructor(string memory _name, string memory _symbol)
  ERC20(_name, _symbol)
  ERC20Permit(_name)
{}
```

* **Arguments:**
  * `_name` is the name of the token.
  * `_symbol` is the symbol that the token should be represented by.


## Write

| Function                                                            | Definition                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`mint`**](write/mint.md)                                 | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _account</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _projectId</code></li></ul> |
| [**`burn`**](write/burn.md)                                 | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _account</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _projectId</code></li></ul> |
| [**`transferOwnership`**](write/transferownership.md)                                 | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _newOwner</code></li></ul> |

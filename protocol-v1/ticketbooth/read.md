# Read

###

### balanceOf

The total balance of tickets a holder has for a specified project, including staked and unstaked tickets.

**Params:**

* **holder:** The ticket holder to get a balance for.
* **_projectId:** The project to get the \`_hodler\`s balance of.

**Returns:** The balance.

```lua
function balanceOf(address _holder, uint256 _projectId)
        external
        view
        returns (uint256 _result);
```

###

### totalSupplyOf

The total supply of tickets for each project, including staked and unstaked tickets.

**Params:**

* **projectId:** The ID of the project to get the total supply of.

**Returns:** The total supply.

```lua
function totalSupplyOf(uint256 _projectId) external view returns (uint256);
```

# totalSupplyOf

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**The total supply of tokens for each project, including claimed and unclaimed tokens.**  
  
Definition:

```solidity
function totalSupplyOf(uint256 _projectId) external view override returns (uint256 supply) { ... }
```

* `_projectId` is the ID of the project to get a total supply of.
* The view function can be accessed externally by anyone. 
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns the total supply.

1. Get a reference to the unclaimed total supply of the project.

   ```solidity
   // Get a reference to the unclaimed total supply of the project.
   supply = unclaimedTotalSupplyOf[_projectId];
   ```

2. Get a reference to the project's tokens.

   ```solidity
   // Get a reference to the project's token.
   IJBToken _token = tokenOf[_projectId];
   ```

3. If the project has issued a token, add it's total supply to the total.

   ```solidity
   // If the project has issued a token, add it's total supply to the total.
   if (_token != IJBToken(address(0))) supply = supply + _token.totalSupply();
   ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The total supply of tokens for each project, including claimed and unclaimed tokens.

  @param _projectId The ID of the project to get the total supply of.

  @return supply The total supply.
*/
function totalSupplyOf(uint256 _projectId) external view override returns (uint256 supply) {
  // Get a reference to the unclaimed total supply of the project.
  supply = unclaimedTotalSupplyOf[_projectId];

  // Get a reference to the project's token.
  IJBToken _token = tokenOf[_projectId];

  // If the project has issued a token, add it's total supply to the total.
  if (_token != IJBToken(address(0))) supply = supply + _token.totalSupply();
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}


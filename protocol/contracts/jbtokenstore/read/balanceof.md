# balanceOf

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**The total balance of token a holder has for a specified project, including claimed and unclaimed tokens.**  
  
Definition:

```solidity
function balanceOf(address _holder, uint256 _projectId)
  external
  view
  override
  returns (uint256 balance) { ... }
```

* `_holder` is the address who's balance is being queried.
* `_projectId` is the ID of the project to get a total supply of.
* The view function can be accessed externally by anyone. 
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns the project token balance of the holder.

1. Get a reference to the holder's unclaimed balance for the project.

   ```solidity
   // Get a reference to the holder's unclaimed balance for the project.
   balance = unclaimedBalanceOf[_holder][_projectId];
   ```

2. Get a reference to the project's token.

   ```solidity
   // Get a reference to the project's token.
   IJBToken _token = tokenOf[_projectId];
   ```

3. If the project has issued a token, add the holder's balance to the total.

   ```solidity
   // If the project has issued a token, add the holder's balance to the total.
   if (_token != IJBToken(address(0))) balance = balance + _token.balanceOf(_holder);
   ```
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice 
  The total balance of token a holder has for a specified project, including claimed and unclaimed tokens.

  @param _holder The token holder to get a balance for.
  @param _projectId The project to get the `_hodler`s balance of.

  @return balance The balance.
*/
function balanceOf(address _holder, uint256 _projectId)
  external
  view
  override
  returns (uint256 balance)
{
  // Get a reference to the holder's unclaimed balance for the project.
  balance = unclaimedBalanceOf[_holder][_projectId];

  // Get a reference to the project's token.
  IJBToken _token = tokenOf[_projectId];

  // If the project has issued a token, add the holder's balance to the total.
  if (_token != IJBToken(address(0))) balance = balance + _token.balanceOf(_holder);
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


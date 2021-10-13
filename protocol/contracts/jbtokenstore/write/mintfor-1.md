# mintFor

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**Mint new tokens.**

_Only a project's current controller can mint its tokens._

Definition:

```solidity
function mintFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) { ... }
```

* Arguments:
  * `_holder` is the address receiving the new tokens.
  * `_projectId` is the ID of the project to which the tokens belong.
  * `_amount` is the amount of tokens to mint.
  * `_preferClaimedTokens` is a flag indicating whether there's a preference for ERC20's to be claimed automatically if they have been issued.
* Through the [`onlyController`](../../jbutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`. 
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.



1. Get a reference to the current splits set for the specified `_projectId` 's `_domain`, within the specified `_group`.   

   ```solidity
   ```

   _Internal references:_

   * [`_splitsOf`](../properties/_splitsof.md)


2. 
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice 
  Mint new tokens.

  @dev
  Only a project's current controller can mint its tokens.

  @param _holder The address receiving the new tokens.
  @param _projectId The ID of the project to which the tokens belong.
  @param _amount The amount of tokens to mint.
  @param _preferClaimedTokens A flag indicating whether there's a preference for ERC20's to be claimed automatically if they have been issued.
*/
function mintFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) {
  // An amount must be specified.
  require(_amount > 0, '0x22: NO_OP');

  // Get a reference to the project's ERC20 tokens.
  IJBToken _token = tokenOf[_projectId];

  // If there exists ERC-20 tokens and the caller prefers these claimed tokens or the project requires it.
  bool _shouldClaimTokens = (requireClaimFor[_projectId] || _preferClaimedTokens) &&
    _token != IJBToken(address(0));

  if (_shouldClaimTokens) {
    // Mint the equivalent amount of ERC20s.
    _token.mint(_holder, _amount);
  } else {
    // Add to the unclaimed balance and total supply.
    unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] + _amount;
    unclaimedTotalSupplyOf[_projectId] = unclaimedTotalSupplyOf[_projectId] + _amount;
  }

  emit Mint(_holder, _projectId, _amount, _shouldClaimTokens, _preferClaimedTokens, msg.sender);
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




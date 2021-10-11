# burnFrom

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**Burns tokens.**

_Only a project's current controller can burn its tokens._

Definition:

```solidity
function burnFrom(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) { ... }
```

* `_holder` is the address address that owns the tokens being burned.
* `_projectId` is the ID of the project to which the burned tokens belong.
* `_amount` is the amount of tokens to burn.
* `_preferClaimedTokens` is a flag indicating if there's a preference to burn tokens that have been converted to ERC-20s.
* Through the [`onlyController`](../../jbutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`. 
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice 
  Burns tokens.

  @dev
  Only a project's current controller can burn its tokens.

  @param _holder The address that owns the tokens being burned.
  @param _projectId The ID of the project to which the burned tokens belong.
  @param _amount The amount of tokens to burn.
  @param _preferClaimedTokens A flag indicating if there's a preference to burn tokens that have been converted to ERC-20s
*/
function burnFrom(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) {
  // Get a reference to the project's ERC20 tokens.
  IJBToken _token = tokenOf[_projectId];

  // Get a reference to the number of unclaimed tokens internally accounted for.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // Get a reference to the number of tokens there are.
  uint256 _claimedBalance = _token == IJBToken(address(0)) ? 0 : _token.balanceOf(_holder);

  // There must be enough tokens.
  // Prevent potential overflow by not relying on addition.
  require(
    (_amount < _claimedBalance && _amount < _unclaimedBalance) ||
      (_amount >= _claimedBalance && _unclaimedBalance >= _amount - _claimedBalance) ||
      (_amount >= _unclaimedBalance && _claimedBalance >= _amount - _unclaimedBalance),
    '0x23: INSUFFICIENT_FUNDS'
  );

  // The amount of tokens to burn.
  uint256 _claimedTokensToBurn;

  // If there's no balance, redeem no tokens.
  if (_claimedBalance == 0) {
    _claimedTokensToBurn = 0;
    // If prefer converted, redeem tokens before redeeming unclaimed tokens.
  } else if (_preferClaimedTokens) {
    _claimedTokensToBurn = _claimedBalance >= _amount ? _amount : _claimedBalance;
    // Otherwise, redeem unclaimed tokens before claimed tokens.
  } else {
    _claimedTokensToBurn = _unclaimedBalance >= _amount ? 0 : _amount - _unclaimedBalance;
  }

  // The amount of unclaimed tokens to redeem.
  uint256 _unclaimedTokensToBurn = _amount - _claimedTokensToBurn;

  // burn the tokens.
  if (_claimedTokensToBurn > 0) _token.burn(_holder, _claimedTokensToBurn);
  if (_unclaimedTokensToBurn > 0) {
    // Reduce the holders balance and the total supply.
    unclaimedBalanceOf[_holder][_projectId] =
      unclaimedBalanceOf[_holder][_projectId] -
      _unclaimedTokensToBurn;
    unclaimedTotalSupplyOf[_projectId] =
      unclaimedTotalSupplyOf[_projectId] -
      _unclaimedTokensToBurn;
  }

  emit Burn(_holder, _projectId, _amount, _unclaimedBalance, _preferClaimedTokens, msg.sender);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}


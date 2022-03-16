# burnFrom

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Burns a project's tokens.**

_Only a project's current controller can burn its tokens._

### Definition

```solidity
function burnFrom(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) { ... }
```

* Arguments:
  * `_holder` is the address that owns the tokens being burned.
  * `_projectId` is the ID of the project to which the burned tokens belong.
  * `_amount` is the amount of tokens to burn.
  * `_preferClaimedTokens` is a flag indicating whether there's a preference for tokens to burned from the `_holder`s wallet if the project currently has a token contract attached.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
* The function doesn't return anything.

### Body

1.  Get a reference to the project's current token.

    ```solidity
    // Get a reference to the project's current token.
    IJBToken _token = tokenOf[_projectId];
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
2.  Get a reference to the amount of unclaimed project tokens the holder has.

    ```solidity
    // Get a reference to the amount of unclaimed project tokens the holder has.
    uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
3.  Get a reference to the amount of the project's tokens the holder has in their wallet. If the project does not yet have tokens issued, the holder must not have a claimed balance.

    ```solidity
    // Get a reference to the amount of the project's current token the holder has in their wallet.
    uint256 _claimedBalance = _token == IJBToken(address(0))
      ? 0
      : _token.balanceOf(_holder, _projectId);
    ```
4.  Make sure the holder has enough tokens to burn. This is true if either the amount to burn is less than both the holder's claimed balance and unclaimed balance, if the amount is greater than the claimed balance and there are enough unclaimed tokens to cover the difference, or if the amount is greater than the unclaimed balance and there are enough claimed tokens to cover the difference.

    ```solidity
    // There must be adequate tokens to burn across the holder's claimed and unclaimed balance.
    if (
      (_amount >= _claimedBalance || _amount >= _unclaimedBalance) &&
      (_amount < _claimedBalance || _unclaimedBalance < _amount - _claimedBalance) &&
      (_amount < _unclaimedBalance || _claimedBalance < _amount - _unclaimedBalance)
    ) revert INSUFFICIENT_FUNDS();
    ```
5.  Find the amount of claimed tokens that should be burned. This will be 0 if the holder has no claimed balance, an amount up to the holder's claimed balance if there is a preference for burning claimed tokens, or the difference between the amount being burned and the holder's unclaimed balance otherwise.

    ```solidity
    // The amount of tokens to burn.
    uint256 _claimedTokensToBurn;

    // If there's no balance, redeem no tokens.
    if (_claimedBalance == 0)
      _claimedTokensToBurn = 0;
      // If prefer converted, redeem tokens before redeeming unclaimed tokens.
    else if (_preferClaimedTokens)
      _claimedTokensToBurn = _claimedBalance < _amount ? _claimedBalance : _amount;
      // Otherwise, redeem unclaimed tokens before claimed tokens.
    else _claimedTokensToBurn = _unclaimedBalance < _amount ? _amount - _unclaimedBalance : 0;
    ```
6.  The amount of unclaimed tokens to burn is necessarily the amount of tokens to burn minus the amount of claimed tokens to burn.

    ```solidity
    // The amount of unclaimed tokens to redeem.
    uint256 _unclaimedTokensToBurn = _amount - _claimedTokensToBurn;
    ```
7.  If there are claimed tokens to burn, burn them from the holder's wallet.

    ```solidity
    // Burn the claimed tokens.
    if (_claimedTokensToBurn > 0) _token.burn(_projectId, _holder, _claimedTokensToBurn);
    ```

    _External references:_

    * [`burn`](../../jbtoken/write/burn.md)
8.  If there are unclaimed tokens to burn, subtract the amount from the `unclaimedBalanceOf` the holder for the project, and from the `unclaimedTotalSupplyOf` the project.

    ```solidity
    // Subtract the tokens from the unclaimed balance and total supply.
    if (_unclaimedTokensToBurn > 0) {
      // Reduce the holders balance and the total supply.
      unclaimedBalanceOf[_holder][_projectId] =
        unclaimedBalanceOf[_holder][_projectId] -
        _unclaimedTokensToBurn;
      unclaimedTotalSupplyOf[_projectId] =
        unclaimedTotalSupplyOf[_projectId] -
        _unclaimedTokensToBurn;
    }
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
    * [`unclaimedTotalSupplyOf`](../properties/unclaimedtotalsupplyof.md)
9.  Emit a `Burn` event with the relevant parameters.

    ```solidity
    emit Burn(
      _holder,
      _projectId,
      _amount,
      _unclaimedBalance,
      _claimedBalance,
      _preferClaimedTokens,
      msg.sender
    );
    ```

    _Event references:_

    * [`Burn`](../events/burn.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Burns a project's tokens.

  @dev
  Only a project's current controller can burn its tokens.

  @param _holder The address that owns the tokens being burned.
  @param _projectId The ID of the project to which the burned tokens belong.
  @param _amount The amount of tokens to burn.
  @param _preferClaimedTokens A flag indicating whether there's a preference for tokens to burned from the `_holder`s wallet if the project currently has a token contract attached.
*/
function burnFrom(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) {
  // Get a reference to the project's current token.
  IJBToken _token = tokenOf[_projectId];

  // Get a reference to the amount of unclaimed project tokens the holder has.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // Get a reference to the amount of the project's current token the holder has in their wallet.
  uint256 _claimedBalance = _token == IJBToken(address(0))
    ? 0
    : _token.balanceOf(_holder, _projectId);

  // There must be adequate tokens to burn across the holder's claimed and unclaimed balance.
  if (
    (_amount >= _claimedBalance || _amount >= _unclaimedBalance) &&
    (_amount < _claimedBalance || _unclaimedBalance < _amount - _claimedBalance) &&
    (_amount < _unclaimedBalance || _claimedBalance < _amount - _unclaimedBalance)
  ) revert INSUFFICIENT_FUNDS();

  // The amount of tokens to burn.
  uint256 _claimedTokensToBurn;

  // If there's no balance, redeem no tokens.
  if (_claimedBalance == 0)
    _claimedTokensToBurn = 0;
    // If prefer converted, redeem tokens before redeeming unclaimed tokens.
  else if (_preferClaimedTokens)
    _claimedTokensToBurn = _claimedBalance < _amount ? _claimedBalance : _amount;
    // Otherwise, redeem unclaimed tokens before claimed tokens.
  else _claimedTokensToBurn = _unclaimedBalance < _amount ? _amount - _unclaimedBalance : 0;

  // The amount of unclaimed tokens to redeem.
  uint256 _unclaimedTokensToBurn = _amount - _claimedTokensToBurn;

  // Burn the claimed tokens.
  if (_claimedTokensToBurn > 0) _token.burn(_projectId, _holder, _claimedTokensToBurn);

  // Subtract the tokens from the unclaimed balance and total supply.
  if (_unclaimedTokensToBurn > 0) {
    // Reduce the holders balance and the total supply.
    unclaimedBalanceOf[_holder][_projectId] =
      unclaimedBalanceOf[_holder][_projectId] -
      _unclaimedTokensToBurn;
    unclaimedTotalSupplyOf[_projectId] =
      unclaimedTotalSupplyOf[_projectId] -
      _unclaimedTokensToBurn;
  }

  emit Burn(
    _holder,
    _projectId,
    _amount,
    _unclaimedBalance,
    _claimedBalance,
    _preferClaimedTokens,
    msg.sender
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                              |
| ------------------------ | -------------------------------------------------------- |
| **`INSUFFICIENT_FUNDS`** | Thrown if the holder doesn't have enough tokens to burn. |
{% endtab %}

{% tab title="Events" %}
| Name                            | Data                                                                                                                                                                                                                                                                          |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Burn`**](../events/burn.md)                             | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>uint256 initialUnclaimedBalance</code></li><li><code>uint256 initialClaimedBalance</code></li><li><code>bool preferClaimedTokens</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

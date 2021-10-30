# \_overflowDuring

Contract: [`JBETHPaymentTerminalStore`](broken-reference)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount that is overflowing when measured from the specified funding cycle.**

_This amount changes as the price of ETH changes in relation to the funding cycle's currency._

## Definition

```solidity
function _overflowDuring(JBFundingCycle memory _fundingCycle) private view returns (uint256) { ... }
```

* Arguments:
  * `_fundingCycle` is the ID of the funding cycle to base the overflow on.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the he overflow of funds.

## Body

1.  Get a reference to the current balance of the funding cycle's project.

    ```solidity
    // Get the current balance of the project.
    uint256 _balanceOf = balanceOf[_fundingCycle.projectId];
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
2.  If the project has no balance, there can't be any overflow.

    ```solidity
    // If there's no balance, there's no overflow.
    if (_balanceOf == 0) return 0;
    ```
3.  Get a reference to the amount of the funding cycle's target that can still be tapped. This is the difference between its target and what has already been tapped during this funding cycle. This value is in terms of the funding cycle's currency.

    ```solidity
    // Get a reference to the amount still withdrawable during the funding cycle.
    uint256 _targetRemaining = _fundingCycle.target - _fundingCycle.tapped;
    ```
4.  Convert the target remaining into ETH using the appropriate price feed.

    ```solidity
    // Convert the _targetRemaining to ETH.
    uint256 _ethTargetRemaining = _targetRemaining == 0
      ? 0 // Get the current price of ETH.
      : PRBMathUD60x18.div(
        _targetRemaining,
        prices.priceFor(_fundingCycle.currency, JBCurrencies.ETH)
      );
    ```

    _Internal references:_

    * [`priceFor`](../../../jbprices/read/pricefor.md)
5.  If the current balance of the project is less than the target remaining, there is no overflow. Otherwise the difference between the project's current balance and the remaining amount is the overflow.

    ```solidity
    // Overflow is the balance of this project minus the amount that can still be withdrawn.
    return _balanceOf < _ethTargetRemaining ? 0 : _balanceOf - _ethTargetRemaining;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the amount that is overflowing when measured from the specified funding cycle.

  @dev
  This amount changes as the price of ETH changes in relation to the funding cycle's currency.

  @param _fundingCycle The ID of the funding cycle to base the overflow on.

  @return overflow The overflow of funds.
*/
function _overflowDuring(JBFundingCycle memory _fundingCycle) private view returns (uint256) {
  // Get the current balance of the project.
  uint256 _balanceOf = balanceOf[_fundingCycle.projectId];

  // If there's no balance, there's no overflow.
  if (_balanceOf == 0) return 0;

  // Get a reference to the amount still withdrawable during the funding cycle.
  uint256 _targetRemaining = _fundingCycle.target - _fundingCycle.tapped;

  // Convert the _targetRemaining to ETH.
  uint256 _ethTargetRemaining = _targetRemaining == 0
    ? 0 // Get the current price of ETH.
    : PRBMathUD60x18.div(
      _targetRemaining,
      prices.priceFor(_fundingCycle.currency, JBCurrencies.ETH)
    );

  // Overflow is the balance of this project minus the amount that can still be withdrawn.
  return _balanceOf < _ethTargetRemaining ? 0 : _balanceOf - _ethTargetRemaining;
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

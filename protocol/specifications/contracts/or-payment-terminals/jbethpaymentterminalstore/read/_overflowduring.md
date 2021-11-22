# \_overflowDuring

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount that is overflowing when measured from the specified funding cycle.**

_This amount changes as the price of ETH changes in relation to the currency being used to measure the distribution limit._

## Definition

```solidity
function _overflowDuring(uint256 _projectId, JBFundingCycle memory _fundingCycle)
  private
  view
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get overflow for.
  * `_fundingCycle` is the ID of the funding cycle to base the overflow on.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the the overflow of funds.

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
3.  Get a reference to the amount of the funding cycle's target that can still be distributed. This is the difference between its distribution limit and what has already been distributed during this funding cycle.

    ```solidity
    // Get a reference to the amount still withdrawable during the funding cycle.
    uint256 _targetRemaining = directory.controllerOf(_projectId).distributionLimitOf(
      _projectId,
      _fundingCycle.configuration,
      terminal
    ) - usedDistributionLimitOf[_projectId][_fundingCycle.number];
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../properties/useddistributionlimitof.md)

    _External references:_

    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/properties/distributionlimitof.md)
4.  Get the currency for the distribution limit.

    ```solidity
    // Get a reference to the current funding cycle's currency for this terminal.
    uint256 _currency = directory.controllerOf(_projectId).currencyOf(
      _projectId,
      _fundingCycle.configuration,
      terminal
    );
    ```

    _External references:_

    * [`currencyOf`](../../../or-controllers/jbcontroller/properties/currencyof.md)
5.  Convert the target remaining into ETH using the appropriate price feed. If the currency is 0, it is assumed that the currency is the same as the token being withdrawn so no conversion is necessary.

    ```solidity
    // Convert the _targetRemaining to ETH.
    uint256 _ethTargetRemaining = _targetRemaining == 0
      ? 0 // Get the current price of ETH.
      : // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
      _currency == 0
      ? _targetRemaining
      : PRBMathUD60x18.div(_targetRemaining, prices.priceFor(_currency, JBCurrencies.ETH));
      );
    ```

    _External references:_

    * [`priceFor`](../../../jbprices/read/pricefor.md)
6.  If the current balance of the project is less than the target remaining, there is no overflow. Otherwise the difference between the project's current balance and the remaining distribution limit is the overflow.

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
  
  @param _projectId The ID of the project to get overflow for.
  @param _fundingCycle The ID of the funding cycle to base the overflow on.

  @return overflow The overflow of funds.
*/
function _overflowDuring(uint256 _projectId, JBFundingCycle memory _fundingCycle)
private
view
returns (uint256) {
  // Get the current balance of the project.
  uint256 _balanceOf = balanceOf[_projectId];

  // If there's no balance, there's no overflow.
  if (_balanceOf == 0) return 0;

  // Get a reference to the amount still withdrawable during the funding cycle.
  uint256 _distributionRemaining = directory.controllerOf(_projectId).distributionLimitOf(
    _projectId,
    _fundingCycle.configuration,
    terminal
  ) - usedDistributionLimitOf[_projectId][_fundingCycle.number];

  // Get a reference to the current funding cycle's currency for this terminal.
  uint256 _currency = directory.controllerOf(_projectId).currencyOf(
    _projectId,
    _fundingCycle.configuration,
    terminal
  );

  // Convert the _distributionRemaining to ETH.
  uint256 _ethDistributionRemaining = _distributionRemaining == 0
    ? 0 // Get the current price of ETH.
    : // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
    _currency == 0
    ? _distributionRemaining
    : PRBMathUD60x18.div(_distributionRemaining, prices.priceFor(_currency, JBCurrencies.ETH));

  // Overflow is the balance of this project minus the amount that can still be distributed.
  return _balanceOf < _ethDistributionRemaining ? 0 : _balanceOf - _ethDistributionRemaining;
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

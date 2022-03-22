# \_overflowDuring

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount that is overflowing when measured from the specified funding cycle.**

_This amount changes as the value of the balance changes in relation to the currency being used to measure the distribution limit._

#### Definition

```solidity
function _overflowDuring(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _balanceCurrency
) private view returns (uint256) { ... }
```

* Arguments:
  * `_terminal` is the terminal for which the overflow is being calculated.
  * `_projectId` is the ID of the project to get overflow for.
  * `_fundingCycle` is the ID of the funding cycle to base the overflow on.
  * `_balanceCurrency` is the currency that the stored balance is expected to be in terms of.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the overflow of funds, as a fixed point number with the same amount of decimals as the specified terminal.

#### Body

1.  Get a reference to the current balance of the project.

    ```solidity
    // Get the current balance of the project.
    uint256 _balanceOf = balanceOf[_terminal][_projectId];
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
2.  If the project has no balance, there can't be any overflow.

    ```solidity
    // If there's no balance, there's no overflow.
    if (_balanceOf == 0) return 0;
    ```
3.  Get a reference to the current distribution limit of the project, along with the currency the limit is in terms of.

    ```solidity
    // Get a reference to the distribution limit during the funding cycle.
    (uint256 _distributionLimit, uint256 _distributionLimitCurrency) = directory
      .controllerOf(_projectId)
      .distributionLimitOf(_projectId, _fundingCycle.configuration, _terminal);
    ```

    _External references:_

    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/properties/distributionlimitof.md)
4.  Get a reference to the amount of the funding cycle's target that can still be distributed. This is the difference between its distribution limit and what has already been distributed during this funding cycle.

    ```solidity
    // Get a reference to the amount still distributable during the funding cycle.
    uint256 _distributionLimitRemaining = _distributionLimit -
      usedDistributionLimitOf[_terminal][_projectId][_fundingCycle.number];
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../properties/useddistributionlimitof.md)

5.  Convert the distribution remaining into the balance's currency using the appropriate price feed. The distribution remaining and balance fixed point numbers should already be using the same number of decimals.

    ```solidity
    // Convert the _distributionRemaining to be in terms of the provided currency.
    if (_distributionLimitRemaining != 0 && _distributionLimitCurrency != _balanceCurrency)
      _distributionLimitRemaining = PRBMath.mulDiv(
        _distributionLimitRemaining,
        10**_MAX_FIXED_POINT_FIDELITY, // Use _MAX_FIXED_POINT_FIDELITY to keep as much of the `_amount.value`'s fidelity as possible when converting.
        prices.priceFor(_distributionLimitCurrency, _balanceCurrency, _MAX_FIXED_POINT_FIDELITY)
      );
    ```

    _Libraries used:_

    * [`JBCurrencies`](../../../../libraries/jbcurrencies.md)
      * `.ETH`

    _Internal references:_

    * [`_MAX_FIXED_POINT_FIDELITY`](../properties/_max_fixed_point_fidelity.md)

    _External references:_

    * [`priceFor`](../../../jbprices/read/pricefor.md)
6.  If the current balance of the project is at most the target remaining, there is no overflow. Otherwise the difference between the project's current balance and the remaining distribution limit is the overflow.

    ```solidity
    // Overflow is the balance of this project minus the amount that can still be distributed.
    return _balanceOf > _distributionLimitRemaining ? _balanceOf - _distributionLimitRemaining : 0;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the amount that is overflowing when measured from the specified funding cycle.

  @dev
  This amount changes as the value of the balance changes in relation to the currency being used to measure the distribution limit.

  @param _terminal The terminal for which the overflow is being calculated.
  @param _projectId The ID of the project to get overflow for.
  @param _fundingCycle The ID of the funding cycle to base the overflow on.
  @param _balanceCurrency The currency that the stored balance is expected to be in terms of.

  @return overflow The overflow of funds, as a fixed point number with the same amount of decimals as the specified terminal.
*/
function _overflowDuring(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _balanceCurrency
) private view returns (uint256) {
  // Get the current balance of the project.
  uint256 _balanceOf = balanceOf[_terminal][_projectId];

  // If there's no balance, there's no overflow.
  if (_balanceOf == 0) return 0;

  // Get a reference to the distribution limit during the funding cycle.
  (uint256 _distributionLimit, uint256 _distributionLimitCurrency) = directory
    .controllerOf(_projectId)
    .distributionLimitOf(_projectId, _fundingCycle.configuration, _terminal);

  // Get a reference to the amount still distributable during the funding cycle.
  uint256 _distributionLimitRemaining = _distributionLimit -
    usedDistributionLimitOf[_terminal][_projectId][_fundingCycle.number];

  // Convert the _distributionRemaining to be in terms of the provided currency.
  if (_distributionLimitRemaining != 0 && _distributionLimitCurrency != _balanceCurrency)
    _distributionLimitRemaining = PRBMath.mulDiv(
      _distributionLimitRemaining,
      10**_MAX_FIXED_POINT_FIDELITY, // Use _MAX_FIXED_POINT_FIDELITY to keep as much of the `_amount.value`'s fidelity as possible when converting.
      prices.priceFor(_distributionLimitCurrency, _balanceCurrency, _MAX_FIXED_POINT_FIDELITY)
    );

  // Overflow is the balance of this project minus the amount that can still be distributed.
  return _balanceOf > _distributionLimitRemaining ? _balanceOf - _distributionLimitRemaining : 0;
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

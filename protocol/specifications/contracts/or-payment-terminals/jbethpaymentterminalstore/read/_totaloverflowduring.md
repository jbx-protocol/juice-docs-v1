# \_totalOverflowDuring

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount that is overflowing across all terminals when measured from the specified funding cycle.**

_This amount changes as the price of ETH changes in relation to the currency being used to measure the distribution limits._

# Definition

```solidity
function _totalOverflowDuring(uint256 _projectId, JBFundingCycle memory _fundingCycle)
  private
  view
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get total overflow for.
  * `_fundingCycle` is the ID of the funding cycle to base the overflow on.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the the overflow of funds.

# Body

1.  Get a reference to all of the project's current terminals.

    ```solidity
    IJBTerminal[] memory _terminals = directory.terminalsOf(_projectId);
    ```

    _Internal references:_

    * [`terminalsOf`](../../../jbdirectory/read/terminalsof.md)
2.  Create references where the total ETH balance across all terminals and the total ETH distribution limits across all terminals will be stored.

    ```solidity
    // Keep a reference to the current eth balance of the project across all terminals, and the current eth distribution limit across all terminals.
    uint256 _ethBalanceOf;
    uint256 _ethDistributionLimitRemaining;
    ```
3.  For each terminal, add its ETH balance to the `_ethBalanceOf` value. Also increment the `_ethDistributionLimitRemaining` value with the distribution limit remaining for the terminal after converting its value from the currency the limit is measured in to ETH. If the currency is 0, it is assumed that the currency is the same as the token being withdrawn so no conversion is necessary. \`

    ```solidity
    for (uint256 _i = 0; _i < _terminals.length; _i++) {
      _ethBalanceOf = _ethBalanceOf + _terminals[_i].ethBalanceOf(_projectId);

      // Get a reference to the amount still withdrawable during the funding cycle.
      uint256 _distributionRemaining = _terminals[_i].remainingDistributionLimitOf(
        _projectId,
        _fundingCycle.configuration,
        _fundingCycle.number
      );

      // Get a reference to the current funding cycle's currency for this terminal.
      uint256 _currency = directory.controllerOf(_projectId).distributionLimitCurrencyOf(
        _projectId,
        _fundingCycle.configuration,
        _terminals[_i]
      );

      // Convert the _distributionRemaining to ETH.
      _ethDistributionLimitRemaining =
        _ethDistributionLimitRemaining +
        (
          _distributionRemaining == 0
            ? 0 // Get the current price of ETH. // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
            : _currency == 0
            ? _distributionRemaining
            : PRBMathUD60x18.div(
              _distributionRemaining,
              prices.priceFor(_currency, JBCurrencies.ETH)
            )
        );
    }
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../properties/useddistributionlimitof.md)

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`

    _External references:_

    * [`ethBalanceOf`](../../jbethpaymentterminal/read/ethbalanceof.md)
    * [`remainingDistributionLimitCurrencyOf`](../../jbethpaymentterminal/read/remainingdistributionlimitcurrencyof.md)
    * [`currencyOf`](../../../or-controllers/jbcontroller/properties/currencyof.md)
    * [`priceFor`](../../../jbprices/read/pricefor.md)
4.  If the current balance of the project is less than the target remaining, there is no overflow. Otherwise the difference between the project's current balance and the remaining distribution limit is the overflow.

    ```solidity
    // Overflow is the balance of this project minus the amount that can still be distributed.
    return
      _ethBalanceOf < _ethDistributionLimitRemaining
        ? 0
        : _ethBalanceOf - _ethDistributionLimitRemaining;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the amount that is overflowing across all terminals when measured from the specified funding cycle.

  @dev
  This amount changes as the price of ETH changes in relation to the currency being used to measure the distribution limits.

  @param _projectId The ID of the project to get total overflow for.
  @param _fundingCycle The ID of the funding cycle to base the overflow on.

  @return overflow The overflow of funds.
*/
function _totalOverflowDuring(uint256 _projectId, JBFundingCycle memory _fundingCycle)
  private
  view
  returns (uint256)
{
  // Get a reference to the project's terminals.
  IJBTerminal[] memory _terminals = directory.terminalsOf(_projectId);

  // Keep a reference to the current eth balance of the project across all terminals, and the current eth distribution limit across all terminals.
  uint256 _ethBalanceOf;
  uint256 _ethDistributionLimitRemaining;

  for (uint256 _i = 0; _i < _terminals.length; _i++) {
    _ethBalanceOf = _ethBalanceOf + _terminals[_i].ethBalanceOf(_projectId);

    // Get a reference to the amount still withdrawable during the funding cycle.
    uint256 _distributionRemaining = _terminals[_i].remainingDistributionLimitOf(
      _projectId,
      _fundingCycle.configuration,
      _fundingCycle.number
    );

    // Get a reference to the current funding cycle's currency for this terminal.
    uint256 _currency = directory.controllerOf(_projectId).distributionLimitCurrencyOf(
      _projectId,
      _fundingCycle.configuration,
      _terminals[_i]
    );

    // Convert the _distributionRemaining to ETH.
    _ethDistributionLimitRemaining =
      _ethDistributionLimitRemaining +
      (
        _distributionRemaining == 0
          ? 0 // Get the current price of ETH. // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
          : _currency == 0
          ? _distributionRemaining
          : PRBMathUD60x18.div(
            _distributionRemaining,
            prices.priceFor(_currency, JBCurrencies.ETH)
          )
      );
  }

  // Overflow is the balance of this project minus the amount that can still be distributed.
  return
    _ethBalanceOf < _ethDistributionLimitRemaining
      ? 0
      : _ethBalanceOf - _ethDistributionLimitRemaining;
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

# \_totalOverflowDuring

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount that is currently overflowing across all of a project's terminals.**

_This amount changes as the value of the balances changes in relation to the currency being used to measure the project's distribution limits._

#### Definition

```solidity
function _currentTotalOverflowOf(
  uint256 _projectId,
  uint256 _decimals,
  uint256 _currency
) private view returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get the total overflow for.
  * `_decimals` is the number of decimals that the fixed point overflow should include.
  * `_currency` is the currency that the overflow should be in terms of.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the total overflow of a project's funds.

#### Body

1.  Get a reference to all of the project's current terminals.

    ```solidity
    // Get a reference to the project's terminals.
    IJBPaymentTerminal[] memory _terminals = directory.terminalsOf(_projectId);
    ```

    _Internal references:_

    * [`terminalsOf`](../../jbdirectory/read/terminalsof.md)
2.  Create references where the total balance across all terminals is be stored in terms of ETH.

    ```solidity
    // Keep a reference to the ETH overflow across all terminals, as a fixed point number with 18 decimals.
    uint256 _ethOverflow;
    ```
3.  For each terminal, add its balance in terms of ETH to the total ETH balance.

    ```solidity
    // Add the current ETH overflow for each terminal.
    for (uint256 _i = 0; _i < _terminals.length; _i++)
      _ethOverflow = _ethOverflow + _terminals[_i].currentEthOverflowOf(_projectId);
    ```

    _External references:_

    * [`currentEthOverflowOf`](../../jbethpaymentterminal/read/currentEthOverflow.md TODO)
4.  If the total overflow is to be returned in a currency other than ETH, make the conversion while maintaining 18 decimals of fidelity.

    ```solidity
    // Convert the ETH overflow to the specified currency if needed, maintaining a fixed point number with 18 decimals.
    uint256 _totalOverflow18Decimal = _currency == JBCurrencies.ETH
      ? _ethOverflow
      : PRBMath.mulDiv(_ethOverflow, 10**18, prices.priceFor(JBCurrencies.ETH, _currency, 18));
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`
    * [`JBCurrencies`](../../../libraries/jbcurrencies.md)
      * `.ETH`

    _External references:_

    * [`priceFor`](../../jbprices/read/pricefor.md)
5.  If the fixed point overflow is to be returned with a number of decimals other than 18, adjust the number accordingly. 

    ```solidity
    // Adjust the decimals of the fixed point number if needed to match the target decimals.
    return
      (_decimals == 18)
        ? _totalOverflow18Decimal
        : JBFixedPointNumber.adjustDecimals(_totalOverflow18Decimal, 18, _decimals);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`
    * [`JBFixedPointNumber`](../../../libraries/jbfixedpointnumber.md)
      * `.adjustDecimals(...)`
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the amount that is currently overflowing across all of a project's terminals. 

  @dev
  This amount changes as the value of the balances changes in relation to the currency being used to measure the project's distribution limits.

  @param _projectId The ID of the project to get the total overflow for.
  @param _decimals The number of decimals that the fixed point overflow should include.
  @param _currency The currency that the overflow should be in terms of.

  @return overflow The total overflow of a project's funds.
*/
function _currentTotalOverflowOf(
  uint256 _projectId,
  uint256 _decimals,
  uint256 _currency
) private view returns (uint256) {
  // Get a reference to the project's terminals.
  IJBPaymentTerminal[] memory _terminals = directory.terminalsOf(_projectId);

  // Keep a reference to the ETH overflow across all terminals, as a fixed point number with 18 decimals.
  uint256 _ethOverflow;

  // Add the current ETH overflow for each terminal.
  for (uint256 _i = 0; _i < _terminals.length; _i++)
    _ethOverflow = _ethOverflow + _terminals[_i].currentEthOverflowOf(_projectId);

  // Convert the ETH overflow to the specified currency if needed, maintaining a fixed point number with 18 decimals.
  uint256 _totalOverflow18Decimal = _currency == JBCurrencies.ETH
    ? _ethOverflow
    : PRBMath.mulDiv(_ethOverflow, 10**18, prices.priceFor(JBCurrencies.ETH, _currency, 18));

  // Adjust the decimals of the fixed point number if needed to match the target decimals.
  return
    (_decimals == 18)
      ? _totalOverflow18Decimal
      : JBFixedPointNumber.adjustDecimals(_totalOverflow18Decimal, 18, _decimals);
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

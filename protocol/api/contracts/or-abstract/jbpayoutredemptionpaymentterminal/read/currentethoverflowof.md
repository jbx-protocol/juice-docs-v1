# currentEthOverflowOf

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the current overflowed amount in this terminal for a specified project, in terms of ETH.**

_The current overflow is represented as a fixed point number with 18 decimals._

### Definition

```solidity
function currentEthOverflowOf(uint256 _projectId) external view override returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the ETH overflow belongs.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The resulting function overrides a function definition from the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) interface.
* The function returns the current amount of ETH overflow that project has in this terminal, as a fixed point number with 18 decimals.

### Body

1.  Get this terminal's current overflow, which is in terms of this terminal's token.

    ```solidity
    // Get this terminal's current overflow.
    uint256 _overflow = store.currentOverflowOf(this, _projectId);
    ```

    _External references:_

    * [`currentOverflowOf`](../../../jbpaymentterminalstore/read/currentoverflowof.md)
2.  If this terminal's fixed point accounting doesn't have 18 decimals, adjust the overflow to have 18 decimals.

    ```solidity
    // Adjust the decimals of the fixed point number if needed to have 18 decimals.
    uint256 _adjustedOverflow = (decimals == 18)
      ? _overflow
      : JBFixedPointNumber.adjustDecimals(_overflow, decimals, 18);
    ```

    _Libraries used:_

    * [`JBFixedPointNumber`](../../../../libraries/jbfixedpointnumber.md)
      * `.adjustDecimals(...)`

3.  If this terminal's currency isn't ETH, convert the overflow to ETH. Return the 18 decimal ETH fixed point overflow value.

    ```solidity
    // Return the amount converted to ETH.
    return
      (currency == JBCurrencies.ETH)
        ? _adjustedOverflow
        : PRBMath.mulDiv(
          _adjustedOverflow,
          10**decimals,
          prices.priceFor(currency, JBCurrencies.ETH, decimals)
        );
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`

    _External references:_

    * [`priceFor`](../../../jbprices/read/pricefor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the current overflowed amount in this terminal for a specified project, in terms of ETH.

  @dev
  The current overflow is represented as a fixed point number with 18 decimals.

  @param _projectId The ID of the project to get overflow for.

  @return The current amount of ETH overflow that project has in this terminal, as a fixed point number with 18 decimals.
*/
function currentEthOverflowOf(uint256 _projectId) external view override returns (uint256) {
  // Get this terminal's current overflow.
  uint256 _overflow = store.currentOverflowOf(this, _projectId);

  // Adjust the decimals of the fixed point number if needed to have 18 decimals.
  uint256 _adjustedOverflow = (decimals == 18)
    ? _overflow
    : JBFixedPointNumber.adjustDecimals(_overflow, decimals, 18);

  // Return the amount converted to ETH.
  return
    (currency == JBCurrencies.ETH)
      ? _adjustedOverflow
      : PRBMath.mulDiv(
        _adjustedOverflow,
        10**decimals,
        prices.priceFor(currency, JBCurrencies.ETH, decimals)
      );
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

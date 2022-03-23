# _currentFeeDiscount

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Get the fee discount from the fee gauge for the specified project.**

#### Definition

```solidity
function _currentFeeDiscount(uint256 _projectId) private view returns (uint256 feeDiscount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a fee discount for.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns thhe fee discount, which should be interpreted as a percentage out MAX_FEE_DISCOUNT.

#### Body

1.  If the protocol project doesn't have a terminal that accepts this terminal's token, no fee can be taken so a max discount should be returned.

    ```solidity
    // Can't take a fee if the protocol project doesn't have a terminal that accepts the token.
    if (directory.primaryTerminalOf(_PROTOCOL_PROJECT_ID, token) == IJBPaymentTerminal(address(0)))
      return JBConstants.MAX_FEE_DISCOUNT;
    ```

    _Libraries used:_

    * [`JBConstants`](../../../../libraries/jbconstants.md)
      * `.MAX_FEE_DISCOUNT`

    _External references:_

    * [`primaryTerminalOf`](../../../jbdirectory/read/primaryterminalof.md)

    _Internal references:_

    * [`token`](../properties/token.md)
2.  If there's a gauge, ask it for the discount. Otherwise, there is no discount. If the gauge reverts, set the discount to 0.

    ```solidity
    // Get the fee discount.
    if( feeGauge == IJBFeeGauge(address(0)) )
      feeDiscount = 0;
    else
      // If the guage reverts, set the discount to 0.
      try feeGauge.currentDiscountFor(_projectId) returns (uint256 discount) {
        feeDiscount = discount;
      } catch {
        feeDiscount = 0;
      }
    ```

    _Internal references:_

    * [`feeGauge`](../properties/feegauge.md)

    _External references:_

    * [`currentDiscountFor`](../../../../interfaces/ijbfeegauge.md)

3.  If there gauge provided an invalid discount, set the discount to 0.

    ```solidity
    // If the fee discount is greater than the max, nullify the discount.
    if (feeDiscount > JBConstants.MAX_FEE_DISCOUNT) feeDiscount = 0;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Get the fee discount from the fee gauge for the specified project.

  @param _projectId The ID of the project to get a fee discount for.
  
  @return feeDiscount The fee discount, which should be interpreted as a percentage out MAX_FEE_DISCOUNT.
*/
function _currentFeeDiscount(uint256 _projectId) private view returns (uint256 feeDiscount) {
  // Can't take a fee if the protocol project doesn't have a terminal that accepts the token.
  if (directory.primaryTerminalOf(_PROTOCOL_PROJECT_ID, token) == IJBPaymentTerminal(address(0)))
    return JBConstants.MAX_FEE_DISCOUNT;

  // Get the fee discount.
  if( feeGauge == IJBFeeGauge(address(0)) )
    feeDiscount = 0;
  else
    // If the guage reverts, set the discount to 0.
    try feeGauge.currentDiscountFor(_projectId) returns (uint256 discount) {
      feeDiscount = discount;
    } catch {
      feeDiscount = 0;
    }

  // If the fee discount is greater than the max, nullify the discount.
  if (feeDiscount > JBConstants.MAX_FEE_DISCOUNT) feeDiscount = 0;
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

 
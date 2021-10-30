# _refundHeldFeesOf

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Refund fees based on the specified amount.**

# Definition

```solidity
function _refundHeldFees(
  uint256 _projectId,
  uint256 _amount,
  uint256 _percent
) private { ... }
```

* Arguments:
  * `_projectId` is the project for which fees are being refunded.
  * `_amount` is the amount to base the refund on.
  * `_percent` is the current fee percent to issue a refund based on.
* The function is private to this contract.
* The function doesn't return anything.

# Body

1.  Get a reference to the amount that can be refunded. This is the same amount that would have been taken as a fee when the provided `_amount` was distributed as a payout or used allowance.

    ```solidity
    // The amount of fees that were taken from an original payout to yield the provided amount.
    uint256 _refundAmount = PRBMath.mulDiv(_amount, _percent + 200, 200) - _amount;
    ```

2.  Get a reference to any held [`JBFee`](../../../data-structures/jbfee.md)'s for the project. 

    ```solidity
    // Get a reference to the project's held fees.
    JBFee[] memory _heldFees = _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)

3.  Delete all of the project's held fees. These will be repopulated if they were not refunded.

    ```solidity
    // Delete the current held fees.
    delete _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)

4.  Loop through each held fee, decrementing the `_refundAmount` as held fees are refunded. If the entire refund amount has been refunded, add the `JBFee` back into the project's held fees so that they can be processed or refunded later. If the `_refundAmount` left is greater than the `JBFee`'s amount, decrement the refunded amount and leave the `JBFee` out of the project's held fees. If only some of the `JBFee`'s amount is needed to cover the rest of the remaining `_refundAmount`, set the refunded amount to 0 after adding the `JBFee` back into the project's held fees having subtracted the remaining refund amount.

    ```solidity
    // Process each fee.
    for (uint256 _i = 0; _i < _heldFees.length; _i++) {
      if (_refundAmount == 0) {
        _heldFeesOf[_projectId].push(_heldFees[_i]);
      } else if (_refundAmount >= _heldFees[_i].amount) {
        _refundAmount = _refundAmount - _heldFees[_i].amount;
      } else {
        _heldFeesOf[_projectId].push(
          JBFee(_heldFees[_i].amount - _refundAmount, _heldFees[_i].beneficiary, _heldFees[_i].memo)
        );
        _refundAmount = 0;
      }
    }
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)

4.  Emit a `Pay` event for the split being iterated on with the relevant parameters.

    ```solidity
    emit Pay(
      _fundingCycle.id,
      _projectId,
      _beneficiary,
      _fundingCycle,
      _amount,
      _weight,
      _tokenCount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`Pay`](../events/pay.md)

5.  Return the funding cycle's ID.

    ```solidity
    return _fundingCycle.id;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Refund fees based on the specified amount.

  @param _projectId The project for which fees are being refunded.
  @param _amount The amount to base the refund on.
  @param _percent The current fee percent to issue a refund based on.
*/
function _refundHeldFees(
  uint256 _projectId,
  uint256 _amount,
  uint256 _percent
) private {
  // The amount of fees that were taken from an original payout to yield the provided amount.
  uint256 _refundAmount = PRBMath.mulDiv(_amount, _percent + 200, 200) - _amount;

  // Get a reference to the project's held fees.
  JBFee[] memory _heldFees = _heldFeesOf[_projectId];

  // Delete the current held fees.
  delete _heldFeesOf[_projectId];

  // Process each fee.
  for (uint256 _i = 0; _i < _heldFees.length; _i++) {
    if (_refundAmount == 0) {
      _heldFeesOf[_projectId].push(_heldFees[_i]);
    } else if (_refundAmount >= _heldFees[_i].amount) {
      _refundAmount = _refundAmount - _heldFees[_i].amount;
    } else {
      _heldFeesOf[_projectId].push(
        JBFee(_heldFees[_i].amount - _refundAmount, _heldFees[_i].beneficiary, _heldFees[_i].memo)
      );
      _refundAmount = 0;
    }
  }
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



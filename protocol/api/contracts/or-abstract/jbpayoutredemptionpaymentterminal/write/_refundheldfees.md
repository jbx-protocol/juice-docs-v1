# _refundHeldFees

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Refund fees based on the specified amount.**

#### Definition

```solidity
function _refundHeldFees(uint256 _projectId, uint256 _amount) private { ... }
```

* Arguments:
  * `_projectId` is the project for which fees are being refunded.
  * `_amount` is the amount to base the refund on, as a fixed point number with the same amount of decimals as this terminal.
* The function is private to this contract.
* The function doesn't return anything.

#### Body

1.  Get a reference to any held [`JBFee`](../../../../data-structures/jbfee.md)'s for the project.

    ```solidity
    // Get a reference to the project's held fees.
    JBFee[] memory _heldFees = _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/_heldfeesof.md)
2.  Delete all of the project's held fees. These will be repopulated if they were not refunded.

    ```solidity
    // Delete the current held fees.
    delete _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/_heldfeesof.md)
3.  Loop through each held fee, decrementing the amount as held fees are refunded. If the entire refund amount has been refunded, add the fee structure back into the project's held fees so that they can be processed or refunded later. If the amount left is greater than the fee structure's amount, decrement the refunded amount and leave the fee structure out of the project's held fees. If only some of the fee structure's amount is needed to cover the rest of the remaining amount, set the amount to 0 after adding the fee structure back into the project's held fees having subtracted the remaining refund amount.

    ```solidity
    // Process each fee.
    for (uint256 _i = 0; _i < _heldFees.length; _i++) {
      if (_amount == 0) _heldFeesOf[_projectId].push(_heldFees[_i]);
      else if (_amount >= _heldFees[_i].amount) _amount = _amount - _heldFees[_i].amount;
      else {
        _heldFeesOf[_projectId].push(
          JBFee(_heldFees[_i].amount - _amount, _heldFees[_i].fee, _heldFees[_i].beneficiary)
        );
        _amount = 0;
      }
    }
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/_heldfeesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Refund fees based on the specified amount.

  @param _projectId The project for which fees are being refunded.
  @param _amount The amount to base the refund on, as a fixed point number with the same amount of decimals as this terminal.
*/
function _refundHeldFees(uint256 _projectId, uint256 _amount) private {
  // Get a reference to the project's held fees.
  JBFee[] memory _heldFees = _heldFeesOf[_projectId];

  // Delete the current held fees.
  delete _heldFeesOf[_projectId];

  // Process each fee.
  for (uint256 _i = 0; _i < _heldFees.length; _i++) {
    if (_amount == 0) _heldFeesOf[_projectId].push(_heldFees[_i]);
    else if (_amount >= _heldFees[_i].amount) _amount = _amount - _heldFees[_i].amount;
    else {
      _heldFeesOf[_projectId].push(
        JBFee(_heldFees[_i].amount - _amount, _heldFees[_i].fee, _heldFees[_i].beneficiary)
      );
      _amount = 0;
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

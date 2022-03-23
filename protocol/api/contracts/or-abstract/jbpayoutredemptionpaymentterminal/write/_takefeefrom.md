# \_takeFeeFrom

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Takes a fee into the platform's project, which has an id of _PROTOCOL_PROJECT_ID.**

#### Definition

```solidity
function _takeFeeFrom(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary,
  uint256 _feeDiscount
) private returns (uint256 feeAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having fees taken from.
  * `_fundingCycle` is the [`JBFundingCycle`](../../../../data-structures/jbfundingcycle.md) during which the fee is being taken.
  * `_amount` is the amount to take a fee from.
  * `_beneficiary` is the address to mint the platforms tokens for.
  * `_feeDiscount` is the amount of discount to apply to the fee, out of the MAX_FEE.
* The function is private to this contract.
* The function returns the amount of the fee taken.

#### Body

1.  Get a reference to the amount that should be taken.

    ```solidity
    // Get the fee discount.
    feeAmount = _feeAmount(_amount, _feeDiscount);
    ```

    _Internal references:_

    * [`_feeAmount`](../read/_feeamount.md)
2.  If the funding cycle is configured to hold fees, add a [`JBFee`](../../../../data-structures/jbfee.md) data structure to the project's stored held fees to be either processed or refunded later. Otherwise, take the fee.

    ```solidity
    _fundingCycle.shouldHoldFees()
      ? _heldFeesOf[_projectId].push(JBFee(_amount, uint32(fee), _beneficiary))
      : _processFee(feeAmount, _beneficiary); // Take the fee.
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)
    * [`_takeFee`](\_takefee.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Takes a fee into the platform's project, which has an id of _PROTOCOL_PROJECT_ID.

  @param _projectId The ID of the project having fees taken from.
  @param _fundingCycle The funding cycle during which the fee is being taken.
  @param _amount The amount of the fee to take, as a floating point number with 18 decimals.
  @param _beneficiary The address to mint the platforms tokens for.
  @param _feeDiscount The amount of discount to apply to the fee, out of the MAX_FEE.

  @return feeAmount The amount of the fee taken.
*/
function _takeFeeFrom(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary,
  uint256 _feeDiscount
) private returns (uint256 feeAmount) {
  feeAmount = _feeAmount(_amount, _feeDiscount);
  _fundingCycle.shouldHoldFees()
    ? _heldFeesOf[_projectId].push(JBFee(_amount, uint32(fee), _beneficiary))
    : _processFee(feeAmount, _beneficiary); // Take the fee.
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

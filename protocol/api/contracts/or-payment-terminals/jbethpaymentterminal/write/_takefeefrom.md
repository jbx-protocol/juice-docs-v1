# \_takeFeeFrom

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Takes a fee into the platform's project, which has an id of 1.**

#### Definition

```solidity
function _takeFeeFrom(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary
) private returns (uint256 feeAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having fees taken from.
  * `_fundingCycle` is the [`JBFundingCycle`](../../../../data-structures/jbfundingcycle.md) during which the fee is being taken.
  * `_amount` is the amount to take a fee from.
  * `_beneficiary` is the address to print the platforms tokens for.
* The function is private to this contract.
* The function returns the amount of the fee taken.

#### Body

1.  Check for a fee discount from the currently set fee gauge if there is one. Otherwise there is no discount.

    ```solidity
    // Get the fee discount.
    uint256 _feeDiscount = feeGauge == IJBFeeGauge(address(0))
      ? 0
      : feeGauge.currentDiscountFor(_projectId);
    ```
2.  If the discount returned is out of bounds, override with no discount.

    ```solidity
    // Set the discounted fee if its valid.
    if (_feeDiscount > JBConstants.MAX_FEE_DISCOUNT) _feeDiscount = 0;
    ```

    _Libraries used:_

    * [`JBConstants`](../../../../libraries/jbconstants.md)
      * `.MAX_FEE_DISCOUNT`
3.  Calculate what the discounted fee is.

    ```solidity
    // Calculate the discounted fee.
    uint256 _discountedFee = fee - PRBMath.mulDiv(fee, _feeDiscount, JBConstants.MAX_FEE_DISCOUNT);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
    * [`JBConstants`](../../../../libraries/jbconstants.md)
      * `.MAX_FEE_DISCOUNT`
4.  Calculate the fee amount from the provided amount using. The fee's percentage should be of the `_amount` without the fee, not of the total `_amount`.

    ```solidity
    // The amount of ETH from the _amount to pay as a fee.
    feeAmount = _amount - PRBMath.mulDiv(_amount, 200, _discountedFee + 200);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
5.  If there's no fee amount, there's no need to take the fee.

    ```solidity
    // Nothing to do if there's no fee to take.
    if (feeAmount == 0) return 0;
    ```
6.  If the funding cycle is configured to hold fees, add a [`JBFee`](../../../../data-structures/jbfee.md) data structure to the project's stored held fees to be either processed or refunded later. Otherwise, take the fee.

    ```solidity
    _fundingCycle.shouldHoldFees()
      ? _heldFeesOf[_projectId].push(JBFee(_amount, uint8(fee), _beneficiary))
      : _takeFee(feeAmount, _beneficiary); // Take the fee.
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)
    * [`_takeFee`](\_takefee.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Takes a fee into the platform's project, which has an id of 1.

  @param _projectId The ID of the project having fees taken from.
  @param _fundingCycle The funding cycle during which the fee is being taken.
  @param _amount The amount to take a fee from.
  @param _beneficiary The address to print the platforms tokens for.

  @return feeAmount The amount of the fee taken.
*/
function _takeFeeFrom(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary
) private returns (uint256 feeAmount) {
  // Get the fee discount.
  uint256 _feeDiscount = feeGauge == IJBFeeGauge(address(0))
    ? 0
    : feeGauge.currentDiscountFor(_projectId);

  // Set the discounted fee if its valid.
  if (_feeDiscount > JBConstants.MAX_FEE_DISCOUNT) _feeDiscount = 0;

  // Calculate the discounted fee.
  uint256 _discountedFee = fee - PRBMath.mulDiv(fee, _feeDiscount, JBConstants.MAX_FEE_DISCOUNT);

  // The amount of ETH from the _amount to pay as a fee.
  feeAmount = _amount - PRBMath.mulDiv(_amount, 200, _discountedFee + 200);

  // Nothing to do if there's no fee to take.
  if (feeAmount == 0) return 0;

  _fundingCycle.shouldHoldFees()
    ? _heldFeesOf[_projectId].push(JBFee(_amount, uint8(fee), _beneficiary))
    : _takeFee(feeAmount, _beneficiary); // Take the fee.
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

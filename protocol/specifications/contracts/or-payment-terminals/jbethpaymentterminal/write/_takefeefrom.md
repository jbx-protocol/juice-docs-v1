# _takeFeeFrom

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Takes a fee into the platform's project, which has an id of 1.**

# Definition

```solidity
function _takeFeeFrom(
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary,
  string memory _memo
) private returns (uint256 feeAmount) { ... }
```

* Arguments:
  * `_fundingCycle` is the [`JBFundingCycle`](../../../../../data-structures/jbfundingcycle.md) during which the fee is being taken.
  * `_amount` is the amount to take a fee from.
  * `_beneficiary` is the address to print the platforms tokens for.
  * `_memo` is a memo to pass along to the emitted event.
* The function is private to this contract.
* The function returns the amount of the fee taken.

# Body

1.  Calculate the fee amount from the provided amount using the funding cycle's fee percent, which is out of 200. The fee's percentage should be of the `_amount` without the fee, not of the total `_amount`.

    ```solidity
    // The amount of ETH from the _amount to pay as a fee.
    feeAmount = _amount - PRBMath.mulDiv(_amount, 200, _fundingCycle.fee + 200);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
2.  If there's no fee amount, there's no need to take the fee.

    ```solidity
    // Nothing to do if there's no fee to take.
    if (feeAmount == 0) return 0;
    ```
3.  If the funding cycle is configured to hold fees, add a [`JBFee`](../../../../data-structures/jbfee.md) data structure to the project's stored held fees to be either processed or refunded later. Otherwise, take the fee.

    ```solidity
    _fundingCycle.shouldHoldFees()
      ? _heldFeesOf[_fundingCycle.projectId].push(JBFee(feeAmount, _beneficiary, _memo)) // Take the fee.
      : _takeFee(feeAmount, _beneficiary, _memo);
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

  @param _fundingCycle The funding cycle during which the fee is being taken. 
  @param _amount The amount to take a fee from.
  @param _beneficiary The address to print the platforms tokens for.
  @param _memo A memo to pass along to the emitted event.

  @return feeAmount The amount of the fee taken.
*/
function _takeFeeFrom(
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  address _beneficiary,
  string memory _memo
) private returns (uint256 feeAmount) {
  // The amount of ETH from the _amount to pay as a fee.
  feeAmount = _amount - PRBMath.mulDiv(_amount, 200, _fundingCycle.fee + 200);

  // Nothing to do if there's no fee to take.
  if (feeAmount == 0) return 0;

  _fundingCycle.shouldHoldFees()
    ? _heldFeesOf[_fundingCycle.projectId].push(JBFee(feeAmount, _beneficiary, _memo)) // Take the fee.
    : _takeFee(feeAmount, _beneficiary, _memo);
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

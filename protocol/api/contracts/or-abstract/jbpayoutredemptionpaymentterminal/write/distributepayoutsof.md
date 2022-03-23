# distributePayoutsOf

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Distributes payouts for a project with the distribution limit of its current funding cycle.**

_Payouts are sent to the preprogrammed splits. Any leftover is sent to the project's owner._

_Anyone can distribute payouts on a project's behalf. The project can preconfigure a wildcard split that is used to send funds to msg.sender. This can be used to incentivize calling this function._

_All funds distributed outside of this contract or any feeless terminals incure the protocol fee._ 

#### Definition

```solidity
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedTokens,
  string calldata _memo
) external virtual override { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having its payouts distributed.
  * `_amount` is the amount of terminal tokens to distribute, as a fixed point number with same number of decimals as this terminal.
  * `_currency` is the expected currency of the amount being distributed. Must match the project's current funding cycle's distribution limit currency.
  * `_minReturnedTokens` is the minimum number of terminal tokens that the `_amount` should be valued at in terms of this terminal's currency, as a fixed point number with the same number of decimals as this terminal.
  * `_memo` is a memo to pass along to the emitted event.
* The function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
* The function returns doesn't return anything.

#### Body

1.  Record the distribution. 

    ```solidity
    // Record the distribution.
    (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordDistributionFor(
      _projectId,
      _amount,
      _currency,
      currency // The balance is in terms of this terminal's currency.
    );
    ```

    _External references:_

    * [`recordDistributionFor`](../../../jbpaymentterminalstore/write/recorddistributionfor.md)
2.  Make sure the distributed amount is at least as much as the minimum expected amount.

    ```solidity
    // The amount being distributed must be at least as much as was expected.
    if (_distributedAmount < _minReturnedTokens) revert INADEQUATE_DISTRIBUTION_AMOUNT();
    ```

3.  Get a reference to the project's owner. The owner will be allocated any funds leftover once splits are settled.

    ```solidity
    // Get a reference to the project owner, which will receive tokens from paying the platform fee
    // and receive any extra distributable funds not allocated to payout splits.
    address payable _projectOwner = payable(projects.ownerOf(_projectId));
    ```

    _External references:_

    * [`ownerOf`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721-ownerOf-uint256-)
4.  The following scoped block is a bit of a hack to prevent a "Stack too deep" error. Define a few variables outside of the scope that'll be set within the scope but later referenced again outside.

    ```solidity
    // Define variables that will be needed outside the scoped section below.
    uint256 _fee;
    uint256 _leftoverDistributionAmount;

    // Scoped section prevents stack too deep. `_feeDiscount` and `_feeEligibleDistributionAmount` only used within scope.
    { ... }
    ```

    1.  Get a reference to the discount that'll be used when applying the fee. If the fee is 0, set the discount to be 100% to simplify subsequent calculations. No fee is the same as a full discount. 

        ```solidity
        // Get the amount of discount that should be applied to any fees taken.
        // If the fee is zero, set the discount to 100% for convinience.
        uint256 _feeDiscount = fee == 0
          ? JBConstants.MAX_FEE_DISCOUNT
          : _currentFeeDiscount(_projectId);
        ```

        _Libraries used:_

        * [`JBConstants`](../../../../libraries/jbconstants.md)
          * `.MAX_FEE_DISCOUNT(...)`

        _Internal references:_

        * [`_currentFeeDiscount`](../read/_currentfeediscount.md)

    2.  Get a reference to the amount of distributed funds from which fees should be taken.

        ```solidity
        // The amount distributed that is eligible for incurring fees.
        uint256 _feeEligibleDistributionAmount;
        ```

    3.  Distribute the amount to all payout splits. Get a reference to any leftover amount, and all amounts sent to splits from which fees should be taken.

        ```solidity
        // Payout to splits and get a reference to the leftover amount after all splits have been paid.
        // Also get a reference to the amount that was distributed to splits from which fees should be taken.
        (_leftoverDistributionAmount, _feeEligibleDistributionAmount) = _distributeToPayoutSplitsOf(
          _projectId,
          _fundingCycle,
          _distributedAmount,
          _feeDiscount
        );
        ```

        _Internal references:_

        * [`_distributeToPayoutSplitsOf`](./_distributetopayoutsplitsof.md)

    4.  Add the leftover distribution amount to the amount from which fees should be taken since those funds will be leaving the ecosystem to the project owner's address.

        ```solidity
        // Leftover distribution amount is also eligible for a fee since the funds are going out of the ecosystem to _beneficiary.
        _feeEligibleDistributionAmount += _leftoverDistributionAmount;
        ```

    5.  Take the fee if needed.

        ```solidity
        // Take the fee.
        _fee = _feeDiscount == JBConstants.MAX_FEE_DISCOUNT ||
          _feeEligibleDistributionAmount == 0
          ? 0
          : _takeFeeFrom(
            _projectId,
            _fundingCycle,
            _feeEligibleDistributionAmount,
            _projectOwner,
            _feeDiscount
          );
        ```

        _Libraries used:_

        * [`JBConstants`](../../../../libraries/jbconstants.md)
          * `.MAX_FEE_DISCOUNT(...)`

        _Internal references:_

        * [`_takeFeeFrom`](./_takefeefrom.md)

    6.  Transfer any leftover amount to the project owner if needed.

        ```solidity
        // Transfer any remaining balance to the project owner.
        if (_leftoverDistributionAmount > 0)
          _transferFrom(
            address(this),
            _projectOwner,
            _leftoverDistributionAmount - _feeAmount(_leftoverDistributionAmount, _feeDiscount)
          );
        ```

        _Internal references:_

        * [`_feeAmount`](./_feeamount.md)

        _Virtual references:_

        * [`_transferFrom`](./_transferfrom.md)

5.  Emit a `DistributePayouts` event with the relevant parameters.

    ```solidity
    emit DistributePayouts(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _projectOwner,
      _amount,
      _distributedAmount,
      _fee,
      _leftoverDistributionAmount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`DistributePayouts`](../events/distributepayouts.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Distributes payouts for a project with the distribution limit of its current funding cycle.

  @dev
  Payouts are sent to the preprogrammed splits. Any leftover is sent to the project's owner.

  @dev
  Anyone can distribute payouts on a project's behalf. The project can preconfigure a wildcard split that is used to send funds to msg.sender. This can be used to incentivize calling this function.

  @dev
  All funds distributed outside of this contract or any feeless terminals incure the protocol fee.

  @param _projectId The ID of the project having its payouts distributed.
  @param _amount The amount of terminal tokens to distribute, as a fixed point number with same number of decimals as this terminal.
  @param _currency The expected currency of the amount being distributed. Must match the project's current funding cycle's distribution limit currency.
  @param _minReturnedTokens The minimum number of terminal tokens that the `_amount` should be valued at in terms of this terminal's currency, as a fixed point number with the same number of decimals as this terminal.
  @param _memo A memo to pass along to the emitted event.
*/
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedTokens,
  string calldata _memo
) external virtual override {
  // Record the distribution.
  (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordDistributionFor(
      _projectId,
      _amount,
      _currency,
      currency // The balance is in terms of this terminal's currency.
    );

  // The amount being distributed must be at least as much as was expected.
  if (_distributedAmount < _minReturnedTokens) revert INADEQUATE_DISTRIBUTION_AMOUNT();

  // Get a reference to the project owner, which will receive tokens from paying the platform fee
  // and receive any extra distributable funds not allocated to payout splits.
  address payable _projectOwner = payable(projects.ownerOf(_projectId));

  // Define variables that will be needed outside the scoped section below.
  uint256 _fee;
  uint256 _leftoverDistributionAmount;

  // Scoped section prevents stack too deep. `_feeDiscount` and `_feeEligibleDistributionAmount` only used within scope.
  {
    // Get the amount of discount that should be applied to any fees taken.
    // If the fee is zero, set the discount to 100% for convinience.
    uint256 _feeDiscount = fee == 0
      ? JBConstants.MAX_FEE_DISCOUNT
      : _currentFeeDiscount(_projectId);

    // The amount distributed that is eligible for incurring fees.
    uint256 _feeEligibleDistributionAmount;

    // Payout to splits and get a reference to the leftover transfer amount after all splits have been paid.
    // Also get a reference to the amount that was distributed to splits from which fees should be taken.
    (_leftoverDistributionAmount, _feeEligibleDistributionAmount) = _distributeToPayoutSplitsOf(
      _projectId,
      _fundingCycle,
      _distributedAmount,
      _feeDiscount
    );

    // Leftover distribution amount is also eligible for a fee since the funds are going out of the ecosystem to _beneficiary.
    _feeEligibleDistributionAmount += _leftoverDistributionAmount;

    // Take the fee.
    _fee = _feeDiscount == JBConstants.MAX_FEE_DISCOUNT ||
      _feeEligibleDistributionAmount == 0
      ? 0
      : _takeFeeFrom(
        _projectId,
        _fundingCycle,
        _feeEligibleDistributionAmount,
        _projectOwner,
        _feeDiscount
      );

    // Transfer any remaining balance to the project owner.
    if (_leftoverDistributionAmount > 0)
      _transferFrom(
        address(this),
        _projectOwner,
        _leftoverDistributionAmount - _feeAmount(_leftoverDistributionAmount, _feeDiscount)
      );
  }

  emit DistributePayouts(
    _fundingCycle.configuration,
    _fundingCycle.number,
    _projectId,
    _projectOwner,
    _amount,
    _distributedAmount,
    _fee,
    _leftoverDistributionAmount,
    _memo,
    msg.sender
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                       | Description                                             |
| ---------------------------- | ------------------------------------------------------- |
| **`INADEQUATE_DISTRIBUTION_AMOUNT`** | Thrown if the amount being distributed is less than the specified minimum. |

{% endtab %}

{% tab title="Events" %}
| Name                                                 | Data                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DistributePayouts`**](../events/distributepayouts.md)             | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 distributedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 beneficiaryDistributionAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                       |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

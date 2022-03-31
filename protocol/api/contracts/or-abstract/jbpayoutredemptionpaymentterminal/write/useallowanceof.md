# useAllowanceOf

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to send funds from its overflow up to the preconfigured allowance.**

_Only a project's owner or a designated operator can use its allowance._

_Incurs the protocol fee._

#### Definition

```solidity
function useAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedTokens,
  address payable _beneficiary,
  string memory _memo
)
  external
  virtual
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.USE_ALLOWANCE) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to use the allowance of.
  * `_amount` is the amount of terminal tokens to use from this project's current allowance, as a fixed point number with the same amount of decimals as this terminal.
  * `_currency` is the expected currency of the amount being distributed. Must match the project's current funding cycle's overflow allowance currency.
  * `_minReturnedTokens` is the minimum number of tokens that the `_amount` should be valued at in terms of this terminal's currency, as a fixed point number with the same amount of decimals as this terminal.
  * `_beneficiary` is the address to send the funds to.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the [`JBOperations.USE_ALLOWANCE`](../../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
* The function doesn't return anything.

#### Body

1.  Record the use of the allowed funds.

    ```solidity
    // Record the use of the allowance.
    (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordUsedAllowanceOf(
      _projectId,
      _amount,
      _currency,
      currency // The balance is in terms of this terminal's currency.
    );
    ```

    _External references:_

    * [`recordUsedAllowanceOf`](../../../jbpaymentterminalstore/write/recordusedallowanceof.md)
2.  Make sure the distributed amount is at least as much as the specified minimum.

    ```solidity
    // The amount being withdrawn must be at least as much as was expected.
    if (_distributedAmount < _minReturnedTokens) revert INADEQUATE_DISTRIBUTION_AMOUNT();
    ```

3.  The following scoped block is a bit of a hack to prevent a "Stack too deep" error. Define a variable outside of the scope that'll be set within the scope but later referenced again outside.

    ```solidity
    // Define variables that will be needed outside the scoped section below.
    // Keep a reference to the fee amount that was paid.
    uint256 _feeAmount;

    // Scoped section prevents stack too deep. `_projectOwner`, `_feeDiscount`, and `_netAmount` only used within scope.
    { ... }
    ```

    1.  Get a reference to the project owner, who will be the beneficiary of the paid fee.

        ```solidity
        // Get a reference to the project owner, which will receive tokens from paying the platform fee.
        address _projectOwner = projects.ownerOf(_projectId);
        ```

        _External references:_

        * [`ownerOf`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721-ownerOf-uint256-)

    2.  Get a reference to the discount that'll be used when applying the fee. If the fee is 0, set the discount to be 100% to simplify subsequent calculations. No fee is the same as a full discount. 

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

    3.  Take the fee if needed.

        ```solidity
        // Take a fee from the `_distributedAmount`, if needed.
        _feeAmount = _feeDiscount == JBConstants.MAX_FEE_DISCOUNT
          ? 0
          : _takeFeeFrom(_projectId, _fundingCycle, _distributedAmount, _projectOwner, _feeDiscount);
        ```

        _Libraries used:_

        * [`JBConstants`](../../../../libraries/jbconstants.md)
          * `.MAX_FEE_DISCOUNT(...)`

        _Internal references:_

        * [`_takeFeeFrom`](./_takefeefrom.md)

    4.  Send the net amount to the beneficiary if needed.

        ```solidity
        // The net amount is the withdrawn amount without the fee.
        uint256 _netAmount = _distributedAmount - _fee;

        // Transfer any remaining balance to the beneficiary.
        if (_netAmount > 0) _transferFrom(address(this), _beneficiary, _netAmount);
        ```

        _Virtual references:_

        * [`_transferFrom`](./_transferfrom.md)

4.  Emit a `UseAllowance` event with the relevant parameters.

    ```solidity
    emit UseAllowance(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _beneficiary,
      _amount,
      _distributedAmount,
      _fee,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`UseAllowance`](../events/useallowance.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows a project to send funds from its overflow up to the preconfigured allowance.

  @dev
  Only a project's owner or a designated operator can use its allowance.
  
  @dev
  Incurs the protocol fee.

  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount of terminal tokens to use from this project's current allowance, as a fixed point number with the same amount of decimals as this terminal.
  @param _currency The expected currency of the amount being distributed. Must match the project's current funding cycle's overflow allowance currency.
  @param _minReturnedTokens The minimum number of tokens that the `_amount` should be valued at in terms of this terminal's currency, as a fixed point number with the same amount of decimals as this terminal.
  @param _beneficiary The address to send the funds to.
  @param _memo A memo to pass along to the emitted event.
*/
function useAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedTokens,
  address payable _beneficiary,
  string memory _memo
)
  external
  virtual
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.USE_ALLOWANCE)
{
  // Record the use of the allowance.
  (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordUsedAllowanceOf(
      _projectId,
      _amount,
      _currency,
      currency // The balance is in terms of this terminal's currency.
    );

  // Define variables that will be needed outside the scoped section below.
  // Keep a reference to the fee amount that was paid.
  if (_distributedAmount < _minReturnedTokens) revert INADEQUATE_DISTRIBUTION_AMOUNT();

  // Define variables that will be needed outside the scoped section below.
  uint256 _fee;

  // Scoped section prevents stack too deep. `_projectOwner`, `_feeDiscount`, and `_netAmount` only used within scope.
  {
    // Get a reference to the project owner, which will receive tokens from paying the platform fee.
    address _projectOwner = projects.ownerOf(_projectId);

    // Get the amount of discount that should be applied to any fees taken.
    // If the fee is zero, set the discount to 100% for convinience.
    uint256 _feeDiscount = fee == 0
      ? JBConstants.MAX_FEE_DISCOUNT
      : _currentFeeDiscount(_projectId);

    // Take a fee from the `_distributedAmount`, if needed.
    _fee = _feeDiscount == JBConstants.MAX_FEE_DISCOUNT
      ? 0
      : _takeFeeFrom(_projectId, _fundingCycle, _distributedAmount, _projectOwner, _feeDiscount);

    // The net amount is the withdrawn amount without the fee.
    uint256 _netAmount = _distributedAmount - _fee;

    // Transfer any remaining balance to the beneficiary.
    if (_netAmount > 0) _transferFrom(address(this), _beneficiary, _netAmount);
  }

  emit UseAllowance(
    _fundingCycle.configuration,
    _fundingCycle.number,
    _projectId,
    _beneficiary,
    _amount,
    _distributedAmount,
    _fee,
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
| Name                                            | Data                                                                                                                                                                                                                                                                                                                                                                |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`UseAllowance`**](../events/useallowance.md)                       | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 distributedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                                                            |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

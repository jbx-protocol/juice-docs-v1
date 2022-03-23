# processFees

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Process any fees that are being held for the project.**

_Only a project owner, an operator, or the contract's owner can process held fees._

#### Definition

```solidity
function processFees(uint256 _projectId)
  external
  virtual
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.PROCESS_FEES,
    msg.sender == owner()
  )
  nonReentrant { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whos held fees should be processed.
* Through the [`requirePermissionAllowingOverride`](../../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the [`JBOperations.PROCESS_FEES`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`, or from the owner of this contract.
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
* The function doesn't return anything.

#### Body

1.  Get a reference to all held fees for the project.

    ```solidity
    // Get a reference to the project's held fees.
    JBFee[] memory _heldFees = _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldFeesOf.md)
2.  Remove all fees.

    ```solidity
    // Delete the held fee's now that they've been processed.
    delete _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldFeesOf.md)
2.  Iterate through the array. Take fee's for each [`JBFee`](../../../../data-structures/jbfee.md) data structure.

    ```solidity
    // Process each fee.
    for (uint256 _i = 0; _i < _heldFees.length; _i++)
      _processFee(
        _heldFees[_i].amount -
          PRBMath.mulDiv(
            _heldFees[_i].amount,
            JBConstants.MAX_FEE,
            _heldFees[_i].fee + JBConstants.MAX_FEE
          ),
        _heldFees[_i].beneficiary
      );
    ```

    _Internal references:_

    * [`_processFee`](_processfee.md)
4.  Emit a `ProcessFees` event with the relevant parameters.

    ```solidity
    emit ProcessFees(_projectId, _heldFees, msg.sender);
    ```

    _Event references:_

    * [`ProcessFees`](../events/processfees.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Process any fees that are being held for the project.

  @dev
  Only a project owner, an operator, or the contract's owner can process held fees.

  @param _projectId The ID of the project whos held fees should be processed.
*/
function processFees(uint256 _projectId)
  external
  virtual
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.PROCESS_FEES,
    msg.sender == owner()
  )
{
  // Get a reference to the project's held fees.
  JBFee[] memory _heldFees = _heldFeesOf[_projectId];

  // Delete the held fees.
  delete _heldFeesOf[_projectId];

  // Process each fee.
  for (uint256 _i = 0; _i < _heldFees.length; _i++)
    _processFee(
      _heldFees[_i].amount -
        PRBMath.mulDiv(
          _heldFees[_i].amount,
          JBConstants.MAX_FEE,
          _heldFees[_i].fee + JBConstants.MAX_FEE
        ),
      _heldFees[_i].beneficiary
    );

  emit ProcessFees(_projectId, _heldFees, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                          | Data                                                                                                                                                       |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ProcessFees`**](../events/processfees.md)                         | <ul><li><code>[`JBFee`](../../../../data-structures/jbfee.md)[] fees</code></li><li><code>uint256 indexed projectId</code></li></ul>                                                                                                                                                                                                                                                                                                                         |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

# useAllowanceOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../interfaces/ijbethterminalof.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to send funds from its overflow up to the preconfigured allowance.**

_Only a token's holder or a designated operator can redeem it._

# Definition

```solidity
function useAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  address payable _beneficiary
)
  external
  override
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.USE_ALLOWANCE)
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to use the allowance of.
  * `_amount` is the amount of the allowance to use.
  * `_beneficiary` is the address to send the funds to.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.USE_ALLOWANCE` permission by the project owner for the provided `_projectId`.
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function returns the ID of the funding cycle during which the allowance was use.

# Body

1.  Record the use of the allowed funds.

    ```solidity
    // Record the use of the allowance.
    (JBFundingCycle memory _fundingCycle, uint256 _withdrawnAmount) = store.recordUsedAllowanceOf(
      _projectId,
      this,
      _amount,
      _currency,
      _minReturnedWei
    );
    ```

    _External references:_

    * [`recordUsedAllowanceOf`](../../jbethpaymentterminalstore/write/recordusedallowanceof.md)

2.  Get a reference to the project's owner to which the tokens generated from paying platform fees will be sent.

    ```solidity
    // Get a reference to the project owner, which will receive tokens from paying the platform fee
    // and receive any extra distributable funds not allocated to payout splits.
    address payable _projectOwner = payable(projects.ownerOf(_projectId));
    ```

    _External references:_

    * [`ownerOf`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/46ce0cfa3323a2787864f884b3c12960bc53b233/contracts/token/ERC721/ERC721.sol#L70)

3.  Get a reference to the project's handle, which will be included in the emitted event.

    ```solidity
    // Get a reference to the handle of the project paying the fee and sending payouts.
    bytes32 _handle = projects.handleOf(_projectId);
    ```

    _External references:_

    * [`handleOf`](../../../jbprojects/read/handleof.md)

4.  If the funding cycle during which the distribtion is being made has a fee, and if its project isn't the JuiceboxDAO project with an ID of 1, take a fee from the withdrawal into the JuiceboxDAO project. 

    ```solidity
    // Take a fee from the _withdrawnAmount, if needed.
    // The project's owner will be the beneficiary of the resulting minted tokens from platform project.
    // The platform project's ID is 1.
    uint256 _feeAmount = _fundingCycle.fee == 0 || _projectId == 1
      ? 0
      : _takeFeeFrom(
        _fundingCycle,
        _withdrawnAmount,
        _projectOwner,
        string(bytes.concat('Fee from @', _handle))
      );
    ```

    _Internal references:_

    * [`_takeFeeFrom`](../properties/_takefeefrom.md)

5.  Transfer the amount minus the fees to the beneficiary. 


    ```solidity
    // Transfer any remaining balance to the project owner.
    Address.sendValue(_beneficiary, _withdrawnAmount - _feeAmount);
    ```

6.  Emit a `UseAllowance` event with the relevant parameters.

    ```solidity
    emit UseAllowance(
      _fundingCycle.id,
      _fundingCycle.configured,
      _projectId,
      _beneficiary,
      _withdrawnAmount,
      _feeAmount,
      _withdrawnAmount - _feeAmount,
      msg.sender
    );
    ```

    _Event references:_

    * [`UseAllowance`](../events/useallowance.md)

7.  Return the funding cycle's ID.

    ```solidity
    return _fundingCycle.id;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Allows a project to send funds from its overflow up to the preconfigured allowance.

  @dev 
  Only a project's owner or a designated operator can migrate it.

  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount of the allowance to use.
  @param _beneficiary The address to send the funds to.

  @return The ID of the funding cycle during which the allowance was use.
*/
function useAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  address payable _beneficiary
)
  external
  override
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.USE_ALLOWANCE)
  returns (uint256)
{
  // Record the use of the allowance.
  (JBFundingCycle memory _fundingCycle, uint256 _withdrawnAmount) = store.recordUsedAllowanceOf(
    _projectId,
    this,
    _amount,
    _currency,
    _minReturnedWei
  );

  // Get a reference to the project owner, which will receive tokens from paying the platform fee
  // and receive any extra distributable funds not allocated to payout splits.
  address payable _projectOwner = payable(projects.ownerOf(_projectId));

  // Get a reference to the handle of the project paying the fee and sending payouts.
  bytes32 _handle = projects.handleOf(_projectId);

  // Take a fee from the _withdrawnAmount, if needed.
  // The project's owner will be the beneficiary of the resulting minted tokens from platform project.
  // The platform project's ID is 1.
  uint256 _feeAmount = _fundingCycle.fee == 0 || _projectId == 1
    ? 0
    : _takeFeeFrom(
      _fundingCycle,
      _withdrawnAmount,
      _projectOwner,
      string(bytes.concat('Fee from @', _handle))
    );

  // Transfer any remaining balance to the project owner.
  Address.sendValue(_beneficiary, _withdrawnAmount - _feeAmount);

  emit UseAllowance(
    _fundingCycle.id,
    _fundingCycle.configured,
    _projectId,
    _beneficiary,
    _withdrawnAmount,
    _feeAmount,
    _withdrawnAmount - _feeAmount,
    msg.sender
  );

  return _fundingCycle.id;
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| [**`UseAllowance`**](../events/useallowance.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed configuration</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 transferAmount</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
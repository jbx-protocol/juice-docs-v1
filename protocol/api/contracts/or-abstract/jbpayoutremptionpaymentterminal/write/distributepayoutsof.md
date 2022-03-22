# distributePayoutsOf

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../interfaces/ijbethpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Distributes payouts for a project according to the constraints of its current funding cycle.**

_Payouts are sent to the preprogrammed splits._

_Anyone can distribute payouts on a project's behalf._

#### Definition

```solidity
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  string memory _memo
) external override nonReentrant { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having its payouts distributed.
  * `_amount` is the amount being distributed.
  * `_currency` is the expected currency of the amount being distributed. Must match the project's current funding cycle's currency.
  * `_minReturnedWei` is the minimum number of wei that the amount should be valued at.
* The function can be accessed externally by anyone.
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function returns doesn't return anything.

#### Body

1.  Record the withdrawal

    ```solidity
    // Record the distribution.
    (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordDistributionFor(
      _projectId,
      _amount,
      _currency,
      _minReturnedWei
    );
    ```

    _External references:_

    * [`recordDistributionFor`](../../jbpaymentterminalstore/write/recorddistributionfor.md)
2.  Get a reference to the project's owner. The owner will be allocated any funds leftover once splits are settled.

    ```solidity
    // Get a reference to the project owner, which will receive tokens from paying the platform fee
    // and receive any extra distributable funds not allocated to payout splits.
    address payable _projectOwner = payable(projects.ownerOf(_projectId));
    ```

    _External references:_

    * [`ownerOf`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/46ce0cfa3323a2787864f884b3c12960bc53b233/contracts/token/ERC721/ERC721.sol#L70)
3.  If the funding cycle during which the distribtion is being made has a fee, and if its project isn't the JuiceboxDAO project with an ID of 1, take a fee from the withdrawal into the JuiceboxDAO project.

    ```solidity
    // Take a fee from the _distributedAmount, if needed.
    // The project's owner will be the beneficiary of the resulting minted tokens from platform project.
    // The platform project's ID is 1.
    uint256 _feeAmount = fee == 0 || _projectId == 1
      ? 0
      : _takeFeeFrom(_projectId, _fundingCycle, _distributedAmount, _projectOwner);
    ```

    _Internal references:_

    * [`_takeFeeFrom`](\_takefeefrom.md)
4.  Distribute the withdrawal to the splits specified for the current funding cycle's configurations. The amount to distribute is the withdrawn amount minus any fees that will be withheld. Get a reference to a leftover amount if there is one.

    ```solidity
    // Payout to splits and get a reference to the leftover transfer amount after all mods have been paid.
    // The net transfer amount is the withdrawn amount minus the fee.
    uint256 _leftoverDistributionAmount = _distributeToPayoutSplitsOf(
      _projectId,
      _fundingCycle,
      _distributedAmount - _feeAmount
    );
    ```

    _Internal references:_

    * [`_distributeToPayoutSplitsOf`](\_distributetopayoutsplitsof.md)
5.  If there is any, transfer any leftover amount to the project owner.

    ```solidity
    // Transfer any remaining balance to the project owner.
    if (_leftoverDistributionAmount > 0)
      Address.sendValue(_projectOwner, _leftoverDistributionAmount);
    ```
6.  Emit a `DistributePayouts` event with the relevant parameters.

    ```solidity
    emit DistributePayouts(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _projectOwner,
      _amount,
      _distributedAmount,
      _feeAmount,
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
  Distributes payouts for a project according to the constraints of its current funding cycle.

  @dev
  Payouts are sent to the preprogrammed splits.

  @dev
  Anyone can distribute payouts on a project's behalf.

  @param _projectId The ID of the project having its payouts distributed.
  @param _amount The amount being distributed.
  @param _currency The expected currency of the amount being distributed. Must match the project's current funding cycle's currency.
  @param _minReturnedWei The minimum number of wei that the amount should be valued at.
*/
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  string memory _memo
) external override nonReentrant {
  // Record the distribution.
  (JBFundingCycle memory _fundingCycle, uint256 _distributedAmount) = store.recordDistributionFor(
    _projectId,
    _amount,
    _currency,
    _minReturnedWei
  );

  // Get a reference to the project owner, which will receive tokens from paying the platform fee
  // and receive any extra distributable funds not allocated to payout splits.
  address payable _projectOwner = payable(projects.ownerOf(_projectId));

  // Get a reference to the handle of the project paying the fee and sending payouts.
  bytes32 _handle = projects.handleOf(_projectId);

  // Take a fee from the _distributedAmount, if needed.
  // The project's owner will be the beneficiary of the resulting minted tokens from platform project.
  // The platform project's ID is 1.
  uint256 _feeAmount = fee == 0 || _projectId == 1
    ? 0
    : _takeFeeFrom(_projectId, _fundingCycle, _distributedAmount, _projectOwner);

  // Payout to splits and get a reference to the leftover transfer amount after all mods have been paid.
  // The net transfer amount is the withdrawn amount minus the fee.
  uint256 _leftoverDistributionAmount = _distributeToPayoutSplitsOf(
    _projectId,
    _fundingCycle,
    _distributedAmount - _feeAmount
  );

  // Transfer any remaining balance to the project owner.
  if (_leftoverDistributionAmount > 0)
    Address.sendValue(_projectOwner, _leftoverDistributionAmount);

  emit DistributePayouts(
    _fundingCycle.configuration,
    _fundingCycle.number,
    _projectId,
    _projectOwner,
    _amount,
    _distributedAmount,
    _feeAmount,
    _leftoverDistributionAmount,
    _memo,
    msg.sender
  );
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                                 | Data                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DistributePayouts`**](../events/addtobalance.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address projectOwner</code></li><li><code>uint256 amount</code></li><li><code>uint256 tappedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 projectOwnerTransferAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

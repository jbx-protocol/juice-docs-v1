# \_distributeToPayoutSplitsOf

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Pays out splits for a project's funding cycle configuration.**

# Definition

```solidity
function _distributeToPayoutSplitsOf(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  string memory _memo
) private returns (uint256 leftoverAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project for which payout splits are being distributed.
  * `_fundingCycle` is the [`JBFundingCycle`](../../../../data-structures/jbfundingcycle.md) during which the distribution is being made.
  * `_amount` is the total amount being distributed.
  * `_memo` is a memo to pass along to the emitted events.
* The function is private to this contract.
* The function returns the leftover amount if the splits don't add up to 100%.

# Body

1.  Save the passed in `_amount` as the `leftoverAmount` that will be returned. The subsequent routine will decrement the leftover amount as splits are settled.

    ```solidity
    // Set the leftover amount to the initial amount.
    leftoverAmount = _amount;
    ```
2.  Get a reference to reserved token splits for the current funding cycle configuration of the project.

    ```solidity
    // Get a reference to the project's payout splits.
    JBSplit[] memory _splits = splitsStore.splitsOf(
      _projectId,
      _fundingCycle.configuration,
      JBSplitsGroups.ETH_PAYOUT
    );
    ```

    _External references:_

    * [`splitsOf`](../../../jbsplitsstore/read/splitsof.md)
3.  Loop through each split.

    ```solidity
    //Transfer between all splits.
    for (uint256 _i = 0; _i < _splits.length; _i++) { ... }
    ```
4.  Get a reference to the current split being iterated on.

    ```solidity
    // Get a reference to the mod being iterated on.
    JBSplit memory _split = _splits[_i];
    ```
5.  Get a reference to the payout amount that should be sent to the current split. This amount is the total amount multiplied by the percentage of the split, which is a number out of 10000000.

    ```solidity
    // The amount to send towards mods. Mods percents are out of 10000000.
    uint256 _payoutAmount = PRBMath.mulDiv(_amount, _split.percent, 10000000);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
6.  If there's at least some funds to send to the payout, determine where they should go. If the split has an `allocator` set, send the funds to its `allocate` function, passing along any relevant params. Otherwise if a `projectId` is specified in the split, send the payout to that project and use the split's `beneficiary` as the address that should receive the project's tokens in return. Otherwise, send the funds directly to the `beneficiary` address from the split. Decrement the `leftoverAmount` once the split is settled.

    ```solidity
    if (_payoutAmount > 0) {
      // Transfer ETH to the mod.
      // If there's an allocator set, transfer to its `allocate` function.
      if (_split.allocator != IJBSplitAllocator(address(0))) {
        _split.allocator.allocate{value: _payoutAmount}(
          _payoutAmount,
          JBSplitsGroups.ETH_PAYOUT,
          _projectId,
          _split.projectId,
          _split.beneficiary,
          _split.preferClaimed
        );
        // Otherwise, if a project is specified, make a payment to it.
      } else if (_split.projectId != 0) {
        // Get a reference to the Juicebox terminal being used.
        IJBTerminal _terminal = directory.primaryTerminalOf(_split.projectId, token);

        // The project must have a terminal to send funds to.
        require(_terminal != IJBTerminal(address(0)), '0x4d: BAD_SPLIT');

        // Save gas if this contract is being used as the terminal.
        if (_terminal == this) {
          _pay(
            _payoutAmount,
            _split.projectId,
            _split.beneficiary,
            0,
            _split.preferClaimed,
            _memo,
            bytes('')
          );
        } else {
          _terminal.pay{value: _payoutAmount}(
            _split.projectId,
            _split.beneficiary,
            0,
            _split.preferClaimed,
            _memo,
            bytes('')
          );
        }
      } else {
        // Otherwise, send the funds directly to the beneficiary.
        Address.sendValue(_split.beneficiary, _payoutAmount);
      }

      // Subtract from the amount to be sent to the beneficiary.
      leftoverAmount = leftoverAmount - _payoutAmount;
    }
    ```

    _Internal references:_

    * [`pay`](broken-reference)
    * [`_pay`](\_pay.md)

    _External references:_

    * [`primaryTerminalOf`](../../../jbdirectory/read/primaryterminalof.md)
7.  Refund any held fees. This is useful to allow a project to withdraw funds from the protocol and subsequently add them back without paying eventually having to pay double fees.

    ```solidity
    // Refund any held fees to make sure the project doesn't pay double for funds going in and out of the protocol.
    _refundHeldFees(_projectId, msg.value, _fundingCycle.fee);
    ```

    _Internal references:_

    * [`_refundHeldFees`](\_refundheldfees.md)
8.  Emit a `DistributeToPayoutSplit` event for the split being iterated on with the relevant parameters.

    ```solidity
    emit DistributeToPayoutSplit(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _split,
      _payoutAmount,
      msg.sender
    );
    ```

    _Event references:_

    * [`DistributeToPayoutSplit`](../events/distributetopayoutsplit.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Pays out splits for a project's funding cycle configuration.
  
  @param _projectId The ID of the project for which payout splits are being distributed.
  @param _fundingCycle The funding cycle during which the distribution is being made.
  @param _amount The total amount being distributed.
  @param _memo A memo to pass along to the emitted events.

  @return leftoverAmount If the leftover amount if the splits don't add up to 100%.
*/
function _distributeToPayoutSplitsOf(
  JBFundingCycle memory _fundingCycle,
  uint256 _amount,
  string memory _memo
) private returns (uint256 leftoverAmount) {
  // Set the leftover amount to the initial amount.
  leftoverAmount = _amount;

  // Get a reference to the project's payout splits.
  JBSplit[] memory _splits = splitsStore.splitsOf(
    _projectId,
    _fundingCycle.configuration,
    JBSplitsGroups.ETH_PAYOUT
  );

  //Transfer between all splits.
  for (uint256 _i = 0; _i < _splits.length; _i++) {
    // Get a reference to the mod being iterated on.
    JBSplit memory _split = _splits[_i];

    // The amount to send towards mods. Mods percents are out of 10000000.
    uint256 _payoutAmount = PRBMath.mulDiv(_amount, _split.percent, 10000000);

    if (_payoutAmount > 0) {
      // Transfer ETH to the mod.
      // If there's an allocator set, transfer to its `allocate` function.
      if (_split.allocator != IJBSplitAllocator(address(0))) {
        _split.allocator.allocate{value: _payoutAmount}(
          _payoutAmount,
          JBSplitsGroups.ETH_PAYOUT,
          _projectId,
          _split.projectId,
          _split.beneficiary,
          _split.preferClaimed
        );
        // Otherwise, if a project is specified, make a payment to it.
      } else if (_split.projectId != 0) {
        // Get a reference to the Juicebox terminal being used.
        IJBTerminal _terminal = directory.primaryTerminalOf(_split.projectId, token);

        // The project must have a terminal to send funds to.
        require(_terminal != IJBTerminal(address(0)), '0x4d: BAD_SPLIT');

        // Save gas if this contract is being used as the terminal.
        if (_terminal == this) {
          _pay(
            _payoutAmount,
            _split.projectId,
            _split.beneficiary,
            0,
            _split.preferClaimed,
            _memo,
            bytes('')
          );
        } else {
          _terminal.pay{value: _payoutAmount}(
            _split.projectId,
            _split.beneficiary,
            0,
            _split.preferClaimed,
            _memo,
            bytes('')
          );
        }
      } else {
        // Otherwise, send the funds directly to the beneficiary.
        Address.sendValue(_split.beneficiary, _payoutAmount);
      }

      // Subtract from the amount to be sent to the beneficiary.
      leftoverAmount = leftoverAmount - _payoutAmount;
    }

    emit DistributeToPayoutSplit(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _split,
      _payoutAmount,
      msg.sender
    );
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                | Description                                                                |
| --------------------- | -------------------------------------------------------------------------- |
| **`0x4d: BAD_SPLIT`** | Thrown if the split specifies a project that doesn't have an ETH terminal. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                  | Data                                                                                                                                                                                                                                                                                     |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DistributeToPayoutSplit`**](../events/distributetopayoutsplit.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

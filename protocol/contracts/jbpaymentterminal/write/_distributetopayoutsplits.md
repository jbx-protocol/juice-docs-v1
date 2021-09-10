# \_distributeToPayoutSplits

```javascript
/** 
  @notice
  Pays out the splits.

  @param _fundingCycle The funding cycle during which the distribution is being made.
  @param _amount The total amount being distributed.
  @param _memo A memo to send along with emitted distribution events.

  @return leftoverAmount If the split module percents dont add up to 100%, the leftover amount is returned.

*/
function _distributeToPayoutSplits(
    FundingCycle memory _fundingCycle,
    uint256 _amount,
    string memory _memo
) private returns (uint256 leftoverAmount) {
    // Set the leftover amount to the initial amount.
    leftoverAmount = _amount;

    // Get a reference to the project's payout splits.
    Split[] memory _splits = splitsStore.get(
        _fundingCycle.projectId,
        _fundingCycle.configured,
        SplitsGroups.Payouts
    );

    // If there are no splits, return the full leftover amount.
    if (_splits.length == 0) return leftoverAmount;

    //Transfer between all splits.
    for (uint256 _i = 0; _i < _splits.length; _i++) {
        // Get a reference to the mod being iterated on.
        Split memory _split = _splits[_i];

        // The amount to send towards mods. Mods percents are out of 10000.
        uint256 _payoutAmount = PRBMath.mulDiv(
            _amount,
            _split.percent,
            10000
        );

        if (_payoutAmount > 0) {
            // Transfer ETH to the mod.
            // If there's an allocator set, transfer to its `allocate` function.
            if (_split.allocator != IJBSplitAllocator(address(0))) {
                _split.allocator.allocate{value: _payoutAmount}(
                    _payoutAmount,
                    SplitsGroups.Payouts,
                    _fundingCycle.projectId,
                    _split.projectId,
                    _split.beneficiary,
                    _split.preferUnstaked
                );
            } else if (_split.projectId != 0) {
                // Otherwise, if a project is specified, make a payment to it.

                // Get a reference to the Juicebox terminal being used.
                IJBTerminal _terminal = directory.terminalOf(
                    _split.projectId
                );

                // The project must have a terminal to send funds to.
                require(
                    _terminal != IJBTerminal(address(0)),
                    "JBPaymentTerminal: BAD_SPLIT"
                );

                // Save gas if this contract is being used as the terminal.
                if (address(_terminal) == address(data)) {
                    _pay(
                        _payoutAmount,
                        _split.projectId,
                        _split.beneficiary,
                        0,
                        _split.preferUnstaked,
                        _memo,
                        bytes("")
                    );
                } else {
                    _terminal.pay{value: _payoutAmount}(
                        _split.projectId,
                        _split.beneficiary,
                        0,
                        _split.preferUnstaked,
                        _memo,
                        bytes("")
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
            _fundingCycle.id,
            _fundingCycle.projectId,
            _split,
            _payoutAmount,
            msg.sender
        );
    }
}
```


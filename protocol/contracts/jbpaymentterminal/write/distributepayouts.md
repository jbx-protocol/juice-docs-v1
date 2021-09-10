# distributePayouts

```javascript
/**
  @notice 
  Distributes payouts for a project according to the constraints of its current funding cycle.
  Payouts are sent to the preprogrammed splits. 

  @dev
  Anyone can distribute payouts on a project's behalf.

  @param _projectId The ID of the project having its payouts distributed.
  @param _amount The amount being distributed.
  @param _currency The expected currency of the amount being distributed. Must match the project's current funding cycle's currency.
  @param _minReturnedWei The minimum number of wei that the amount should be valued at.

  @return The ID of the funding cycle during which the distribution was made.
*/
function distributePayouts(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei,
    string memory _memo
) external override nonReentrant returns (uint256) {
    // Record the withdrawal in the data layer.
    (FundingCycle memory _fundingCycle, uint256 _withdrawnAmount) = data
        .recordWithdrawal(_projectId, _amount, _currency, _minReturnedWei);

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
        : _takeFee(
            _withdrawnAmount,
            _fundingCycle.fee,
            _projectOwner,
            string(bytes.concat("Fee from @", _handle))
        );

    // Payout to splits and get a reference to the leftover transfer amount after all mods have been paid.
    // The net transfer amount is the withdrawn amount minus the fee.
    uint256 _leftoverTransferAmount = _distributeToPayoutSplits(
        _fundingCycle,
        _withdrawnAmount - _feeAmount,
        string(bytes.concat("Payout from @", _handle))
    );

    // Transfer any remaining balance to the project owner.
    if (_leftoverTransferAmount > 0)
        Address.sendValue(_projectOwner, _leftoverTransferAmount);

    emit DistributePayouts(
        _fundingCycle.id,
        _projectId,
        _projectOwner,
        _amount,
        _withdrawnAmount,
        _feeAmount,
        _leftoverTransferAmount,
        _memo,
        msg.sender
    );

    return _fundingCycle.id;
}
```


# useAllowance

```javascript
/**
  @notice Allows a project to send funds from its overflow up to the preconfigured allowance.
  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount of the allowance to use.
  @param _beneficiary The address to send the funds to.
*/
function useAllowance(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei,
    address payable _beneficiary
)
    external
    override
    nonReentrant
    requirePermission(
        projects.ownerOf(_projectId),
        _projectId,
        Operations2.UseAllowance
    )
    returns (uint256)
{
    // Record the use of the allowance in the data layer.
    (FundingCycle memory _fundingCycle, uint256 _withdrawnAmount) = data
        .recordUsedAllowance(
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

    // The leftover amount once the fee has been taken.
    uint256 _leftoverTransferAmount = _withdrawnAmount - _feeAmount;

    // Transfer any remaining balance to the project owner.
    if (_leftoverTransferAmount > 0)
        // Send the funds to the beneficiary.
        Address.sendValue(_beneficiary, _leftoverTransferAmount);

    emit UseAllowance(
        _fundingCycle.id,
        _fundingCycle.configured,
        _projectId,
        _beneficiary,
        _withdrawnAmount,
        _feeAmount,
        _leftoverTransferAmount,
        msg.sender
    );

    return _fundingCycle.configured;
}
```


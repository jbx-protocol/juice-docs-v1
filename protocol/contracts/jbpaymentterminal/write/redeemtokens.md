# redeemTokens

```javascript
/**
  @notice
  Addresses can redeem their tokens to claim the project's overflowed ETH, or to trigger rules determined by the project's current funding cycle's data source.

  @dev
  Only a token's holder or a designated operator can redeem it.

  @param _holder The account to redeem tokens for.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of tokens to redeem.
  @param _minReturnedWei The minimum amount of Wei expected in return.
  @param _beneficiary The address to send the ETH to. Send the address this contract to burn the count.
  @param _memo A memo to attach to the emitted event.
  @param _delegateMetadata Bytes to send along to the delegate, if one is provided.

  @return claimAmount The amount of ETH that the tokens were redeemed for, in wei.
*/
function redeemTokens(
    address _holder,
    uint256 _projectId,
    uint256 _tokenCount,
    uint256 _minReturnedWei,
    address payable _beneficiary,
    string memory _memo,
    bytes memory _delegateMetadata
)
    external
    override
    nonReentrant
    requirePermissionAllowingWildcardDomain(
        _holder,
        _projectId,
        Operations.Redeem
    )
    returns (uint256 claimAmount)
{
    // Can't send claimed funds to the zero address.
    require(_beneficiary != address(0), "JBPaymentTerminal: ZERO_ADDRESS");
    {
        // Keep a reference to the funding cycles during which the redemption is being made.
        FundingCycle memory _fundingCycle;

        // Record the redemption in the data layer.
        (_fundingCycle, claimAmount, _memo) = data.recordRedemption(
            _holder,
            _projectId,
            _tokenCount,
            _minReturnedWei,
            _beneficiary,
            _memo,
            _delegateMetadata
        );

        // Send the claimed funds to the beneficiary.
        if (claimAmount > 0) Address.sendValue(_beneficiary, claimAmount);

        emit Redeem(
            _fundingCycle.id,
            _projectId,
            _holder,
            _fundingCycle,
            _beneficiary,
            _tokenCount,
            claimAmount,
            _memo,
            msg.sender
        );
    }
}
```


# \_pay

```javascript
/**
  @notice
  See the documentation for 'pay'.
*/
function _pay(
    uint256 _amount,
    uint256 _projectId,
    address _beneficiary,
    uint256 _minReturnedTokens,
    bool _preferUnstakedTokens,
    string memory _memo,
    bytes memory _delegateMetadata
) private returns (uint256) {
    // Positive payments only.
    require(_amount > 0, "JBPaymentTerminal: BAD_AMOUNT");

    // Cant send tokens to the zero address.
    require(_beneficiary != address(0), "JBPaymentTerminal: ZERO_ADDRESS");

    FundingCycle memory _fundingCycle;
    uint256 _weight;
    uint256 _tokenCount;

    // Record the payment in the data layer.
    (_fundingCycle, _weight, _tokenCount, _memo) = data.recordPayment(
        msg.sender,
        _amount,
        _projectId,
        (_preferUnstakedTokens ? 1 : 0) | uint160(_beneficiary),
        _minReturnedTokens,
        _memo,
        _delegateMetadata
    );

    emit Pay(
        _fundingCycle.id,
        _projectId,
        _beneficiary,
        _fundingCycle,
        _amount,
        _weight,
        _tokenCount,
        _memo,
        msg.sender
    );

    return _fundingCycle.id;
}
```


# addToBalance

```javascript
/**
  @notice
  Receives and allocated funds belonging to the specified project.

  @param _projectId The ID of the project to which the funds received belong.
  @param _memo A memo to include in the emitted event.
*/
function addToBalance(uint256 _projectId, string memory _memo)
    external
    payable
    override
{
    // Amount must be greater than 0.
    require(msg.value > 0, "JBPaymentTerminal: NO_OP");

    // Record the added funds in the data later.
    data.recordAddedBalance(msg.value, _projectId);

    emit AddToBalance(_projectId, msg.value, _memo, msg.sender);
}
```


# \_takeFee

```javascript
/** 
  @notice 
  Takes a fee into the platform's project, which has an id of 1.

  @param _from The amount to take a fee from.
  @param _percent The percent fee to take. Out of 200.
  @param _beneficiary The address to print the platforms tokens for.
  @param _memo A memo to send with the fee.

  @return feeAmount The amount of the fee taken.
*/
function _takeFee(
    uint256 _from,
    uint256 _percent,
    address _beneficiary,
    string memory _memo
) private returns (uint256 feeAmount) {
    // The amount of ETH from the _tappedAmount to pay as a fee.
    feeAmount = _from - PRBMath.mulDiv(_from, 200, _percent + 200);

    // Nothing to do if there's no fee to take.
    if (feeAmount == 0) return 0;

    // Get the terminal for the JuiceboxDAO project.
    IJBTerminal _terminal = directory.terminalOf(1);

    // When processing the admin fee, save gas if the admin is using this contract as its terminal.
    address(_terminal) == address(data) // Use the local pay call.
        ? _pay(feeAmount, 1, _beneficiary, 0, false, _memo, bytes("")) // Use the external pay call of the correct terminal.
        : _terminal.pay{value: feeAmount}(
            1,
            _beneficiary,
            0,
            false,
            _memo,
            bytes("")
        );
}
```


# _transferFrom

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

**Logic to be triggered before transferring tokens from this terminal.**

# Definition

```solidity
/** 
  @notice
  Logic to be triggered before transferring tokens from this terminal.

  @param _to The address to which the transfer is going.
  @param _amount The amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
*/
function _beforeTransferTo(address _to, uint256 _amount) internal virtual;
```

* Arguments:
  * `_to` is the address to which the transfer should go.
  * `_amount` is the amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
* The resulting function is internal to this contract and its inheriters.
* The virtual function must be implemented by inheriters.

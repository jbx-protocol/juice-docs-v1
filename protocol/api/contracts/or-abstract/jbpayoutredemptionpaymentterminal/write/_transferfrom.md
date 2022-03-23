# _transferFrom

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

**Transfers tokens.**

# Definition

```solidity
/** 
  @notice
  Transfers tokens.

  @param _from The address from which the transfer should originate.
  @param _to The address to which the transfer should go.
  @param _amount The amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
*/
function _transferFrom(
  address _from,
  address payable _to,
  uint256 _amount
) internal virtual;
```

* Arguments:
  * `_from` is the address from which the transfer should originate.
  * `_to` is the address to which the transfer should go.
  * `_amount` is the amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
* The resulting function is internal to this contract and its inheriters.
* The virtual function must be implemented by inheriters.

# store

Contract:[`JBETHPaymentTerminal`](../)​‌

**Fees that are being held to be processed later.** 

## Definition

```solidity
/**
  @notice 
  Fees that are being held to be processed later.

  _projectId The ID of the project whos fees are held.
*/
mapping(uint256 => JBFee[]) private _heldFeesOf;
```

* `_projectId` is the ID of the project whos fees are held.
* The resulting view function is private to this contract.
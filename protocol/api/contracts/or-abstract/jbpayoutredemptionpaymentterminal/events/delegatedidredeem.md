# DelegateDidRedeem

Emitted from:

* [`redeemTokensOf`](../write/redeemtokensof.md)

## Definition

```solidity
event DelegateDidRedeem(
  IJBRedemptionDelegate indexed delegate,
  JBDidRedeemData data,
  address caller
);
```

* `delegate` is the [`IJBRedeemDelegate`](../../../../interfaces/ijbredemptiondelegate.md) whos `didRedeem` transaction was triggered.
* `data` is the [`JBDidRedeemData`](../../../../data-structures/jbdidredeemdata.md) that was sent to the `IJBRedeemDelegate`'s `didRedeem` function.
* `caller` is the address that issued the transaction within which the event was emitted.

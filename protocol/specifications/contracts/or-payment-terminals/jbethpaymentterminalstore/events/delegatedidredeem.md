# DelegateDidRedeem

Emitted from:

* [`recordRedemptionFor`](../write/recordredemptionfor.md)

## Definition

```solidity
event DelegateDidRedeem(IJBRedemptionDelegate indexed delegate, JBDidRedeemData data);
```

* `delegate` is the [`IJBRedeemDelegate`](../../../../../../protocol/specifications/interfaces/ijbredeemdelegate.md) whos `didRedeem` function that was triggered.
* `data` is the [`JBDidRedeemData`](../../../../data-structures/jbdidredeemdata.md) that was sent to the `IJBRedeemDelegate`'s `didRedeem` function.

# DelegateDidRedeem

Emitted from:

* [`recordRedemptionFor`](../write/recordredemptionfor.md)

## Definition

```solidity
event DelegateDidRedeem(IJBRedemptionDelegate indexed delegate, JBDidRedeemData data);
```

* `delegate` is the [`IJBRedeemDelegate`](../../../../../protocol/contracts/interfaces/ijbredeemdelegate.sol) whos `didRedeem` function that was triggered.
* `data` is the [`JBDidRedeemData`](../../../../../protocol/contracts/data-structures/jbdidredeemdata.sol) that was sent to the `IJBRedeemDelegate`'s `didRedeem` function.

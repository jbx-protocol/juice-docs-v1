# DelegateDidPay

Emitted from:

* [`recordPaymentFrom`](../write/recordpaymentfrom.md)

## Definition

```solidity
event DelegateDidPay(IJBPayDelegate indexed delegate, JBDidPayData data);
```

* `delegate` is the [`IJBPayDelegate`](../../../../../../protocol/specifications/interfaces/ijbpaydelegate.sol) whos `didPay` function that was triggered.
* `data` is the [`JBDidPayData`](../../../../../../protocol/specifications/data-structures/jbdidpaydata.sol) that was sent to the `IJBPayDelegate`'s `didPay` function.

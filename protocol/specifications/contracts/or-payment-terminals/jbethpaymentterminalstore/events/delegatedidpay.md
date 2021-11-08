# DelegateDidPay

Emitted from:

* [`recordPaymentFrom`](../write/recordpaymentfrom.md)

# Definition

```solidity
event DelegateDidPay(IJBPayDelegate indexed delegate, JBDidPayData data);
```

* `delegate` is the [`IJBPayDelegate`](../../../../interfaces/ijbpaydelegate.md) whos `didPay` function that was triggered.
* `data` is the [`JBDidPayData`](../../../../data-structures/jbdidpaydata.md) that was sent to the `IJBPayDelegate`'s `didPay` function.

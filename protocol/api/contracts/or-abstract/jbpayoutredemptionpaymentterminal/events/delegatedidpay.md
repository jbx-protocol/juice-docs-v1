# DelegateDidPay

Emitted from:

* [`_pay`](../write/_pay.md)

## Definition

```solidity
event DelegateDidPay(IJBPayDelegate indexed delegate, JBDidPayData data, address caller);
```

* `delegate` is the [`IJBPayDelegate`](../../../../interfaces/ijbpaydelegate.md) whos `didPay` transaction was triggered.
* `data` is the [`JBDidPayData`](../../../../data-structures/jbdidpaydata.md) that was sent to the `IJBPayDelegate`'s `didPay` function.
* `caller` is the address that issued the transaction within which the event was emitted.

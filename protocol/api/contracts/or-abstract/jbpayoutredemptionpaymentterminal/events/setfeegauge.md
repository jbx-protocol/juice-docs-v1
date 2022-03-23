# SetFeeGauge

Emitted from:

* [`setFeeGauge`](../write/setfeegauge.md)

## Definition

```solidity
event SetFeeGauge(IJBFeeGauge feeGauge, address caller);
```

* `feeGauge` is the new [`feeGuage`](../../../../interfaces/ijbfeegauge.md).
* `caller` is the address that issued the transaction within which the event was emitted.

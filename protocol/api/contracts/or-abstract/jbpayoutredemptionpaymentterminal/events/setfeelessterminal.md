
# SetFeelessTerminal

Emitted from:

* [`setFeelessTerminal`](../write/setfeelessterminal.md)

## Definition

```solidity
event SetFeelessTerminal(IJBPaymentTerminal indexed terminal, bool indexed flag, address caller);
```

* `terminal` is the terminal that was made feeless or not.
* `flag` is whether or not the terminal was made feeless.
* `caller` is the address that issued the transaction within which the event was emitted.

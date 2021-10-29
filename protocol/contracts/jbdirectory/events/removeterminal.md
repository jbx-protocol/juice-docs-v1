# RemoveTerminal

Emitted from:

* [`removeTerminalOf`](../write/removeterminalof.md)

Definition:

```solidity
event RemoveTerminal(uint256 indexed projectId, IJBTerminal indexed terminal, address caller);
```

* `projectId` is the ID of the project that removed a terminal.
* `terminal` is the address of the terminal that was removed.
* `caller` is the address that issued the transaction within which the event was emitted.

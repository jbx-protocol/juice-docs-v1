# AddTerminal

Emitted from:

* [`addTerminalsOf`](../write/addterminalsof.md)
* [`setPrimaryTerminalOf`](../write/setprimaryterminalof.md)

Definition:

```solidity
event AddTerminal(uint256 indexed projectId, IJBTerminal indexed terminal, address caller);
```

* `projectId` is the ID of the project that added a terminal.
* `terminal` is the address of the terminal that was added.
* `caller` is the address that issued the transaction within which the event was emitted.

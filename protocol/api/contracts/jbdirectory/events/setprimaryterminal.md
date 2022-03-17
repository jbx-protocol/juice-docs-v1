# SetPrimaryTerminal

Emitted from:

* [`setPrimaryTerminalOf`](../write/setprimaryterminalof.md)

Definition:

```solidity
event SetPrimaryTerminal(
  uint256 indexed projectId,
  address indexed token,
  IJBPaymentTerminal indexed terminal,
  address caller
);
```

* `projectId` is the ID of the project that set a primary terminal.
* `token` is the token for which the terminal is the project's primary.
* `terminal` is the address of the terminal that is now the project's primary for the token.
* `caller` is the address that issued the transaction within which the event was emitted.

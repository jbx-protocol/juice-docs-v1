# SetHandle

Emitted from:

* [`setHandleOf`](../write/sethandleof.md)

Definition:

```solidity
event SetHandle(
    uint256 indexed projectId,
    bytes32 indexed handle,
    address caller
);
```

* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's handle was set.
* `handle` is the unique handle that was set.
* `caller` is the address that issued the transaction within which the event was emitted.

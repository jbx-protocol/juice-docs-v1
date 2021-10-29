# RenewHandle

Emitted from:

* [`renewHandleOf`](../write/renewhandleof.md)

## Definition

```solidity
event RenewHandle(
    bytes32 indexed handle,
    uint256 indexed projectId,
    address caller
);
```

* `handle` is the handle that has been renewed.
* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's handle has been renewed.
* `caller` is the address that issued the transaction within which the event was emitted.

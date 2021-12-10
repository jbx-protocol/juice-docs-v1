# ClaimHandle

Emitted from:

* [`claimHandle`](../write/claimhandle.md)

## Definition

```solidity
event ClaimHandle(
    uint256 indexed projectId,
    address indexed transferAddress,
    bytes32 indexed handle,
    address caller
);
```

* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's receiving the transferred handle.
* `transferAddress` is the address to which the handle was originally transferred, and who has now reassigned the handle to the project.
* `handle` is the handle that was claimed.
* `caller` is the address that issued the transaction within which the event was emitted.

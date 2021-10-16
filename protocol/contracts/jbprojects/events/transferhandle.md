# TransferHandle

Emitted from:

* [`transferHandle`](../write/transferhandleof.md)

# Definition

```solidity
event TransferHandle(
    uint256 indexed projectId,
    address indexed transferAddress,
    bytes32 indexed handle,
    bytes32 newHandle,
    address caller
);
```

* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's handle was transferred.
* `transferAddress` is the address that can now reassign the handle to a project.
* `handle` is the handle that was transferred.
* `newHandle` is the unique handle that was set for the project to replace the transferred handle.
* `caller` is the address that issued the transaction within which the event was emitted.

# ChallengeHandle

Emitted from:

* [`challengeHandle`](../write/claimhandle.md)

## Definition

```solidity
event ChallengeHandle(
    bytes32 indexed handle,
    uint256 indexed projectId,
    uint256 challengeExpiry,
    address caller
);
```

* `handle` is the handle that is being challenged. 
* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's handle has been challenged.
* `challengeExpiry` is the time at which the challenged handle can be claimed if it has yet to be renewed.
* `caller` is the address that issued the transaction within which the event was emitted.

# SetUri

Emitted from:

* [`setUriOf`](../write/seturiof.md)

Definition:

```solidity
event SetUri(uint256 indexed projectId, string uri, address caller);
```

* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's URI was set.
* `uri` is the IPFS CID that was set.
* `caller` is the address that issued the transaction within which the event was emitted.

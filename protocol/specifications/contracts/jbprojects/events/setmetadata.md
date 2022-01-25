# SetMetadataCid

Emitted from:

* [`setMetadataCidOf`](../../../../../protocol/specifications/contracts/jbprojects/write/setmetadatacidof.md)

## Definition

```solidity
event SetMetadataCid(uint256 indexed projectId, string uri, address caller);
```

* `projectId` is the token ID of the NFT (ERC-721) that represents the project who's URI was set.
* `uri` is the IPFS CID that was set.
* `caller` is the address that issued the transaction within which the event was emitted.

# Init

Emitted from:

* [`configureFor`](../write/configurefor.md)

# Definition

```solidity
event Init(
  uint256 indexed configuration,
  uint256 indexed projectId,
  uint256 indexed basedOn
);
```

* `configuration` is the funding cycle configuration that was initialized.
* `projectId` is the ID of the project to which the initialized funding cycle belongs.
* `basedOn` is the ID of the funding cycle that the initialized funding cycle is based on.

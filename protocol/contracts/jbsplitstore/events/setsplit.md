# SetSplit

Emitted from:

* [`set`](../write/set.md)

Definition:

```javascript
event SetSplit(
    uint256 indexed projectId,
    uint256 indexed domain,
    uint256 indexed group,
    Split split,
    address caller
);
```

* `projectId` is the ID of the project to which the split belongs.
* `domain` is the namespace that differentiates different split groups for the projectId.
* `group`  is the property that joins multiple splits into one full group.
* `split` is a Split struct.
* `caller` is the address that issued the transaction within which the event was emitted.


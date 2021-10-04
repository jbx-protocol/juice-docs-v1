# Init

Emitted from:

* [`configureFor`](../../jbsplitstore/write/set.md)
* [`tapFrom`](../write/tapfrom.md)

Definition:

```javascript
event Init(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  uint256 indexed basedOn
);
```

* `fundingCycleId` is the ID of the funding cycle that was initialized.
* `projectId` is the ID of the project to which the initialized funding cycle belongs.
* `basedOn` is the ID of the funding cycle that the initialized funding cycle is based on.


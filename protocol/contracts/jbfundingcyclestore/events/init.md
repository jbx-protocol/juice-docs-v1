# Init

Emitted from:

* [`configureFor`](../../jbsplitstore/write/set.md)
* [`tapFrom`](../write/tapfrom.md)

Definition:

```javascript
event Init(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  uint256 indexed number,
  uint256 basedOn,
  uint256 weight,
  uint256 start
);
```

* `fundingCycleId` is the ID of the funding cycle that was initialized.
* `projectId` is the ID of the project to which the initialized funding cycle belongs.
* `number` is the number of the funding cycle that was initialized.
* `basedOn` is the ID of the funding cycle that the initialized funding cycle is based on.
* `weight` is the weight of the newly initialized funding cycle.
* `start` is the time at which the initialized funding cycle starts.


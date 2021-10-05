# ChangeToken

Emitted from:

* [`changeTokenOf`](../write/changetokenof.md)

Definition:

```javascript
event ChangeToken(
  uint256 indexed projectId,
  IJBToken indexed token,
  address indexed owner,
  address caller
);
```

* `projectId` is the project to which the changed token belongs.
* `token` is the new token that is being used by the project.
* `owner` is the new owner of the token that was previously used by the project.
* `caller` is the address that issued the transaction within which the event was emitted.


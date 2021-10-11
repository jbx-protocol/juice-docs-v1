# renewHandleOf

Contract:[`JBProjects`](../)

Interface:`IJBProjects`

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to renew its handle, which cancels any pending challenges.**

_Only a project's owner or operator can renew its handle._\
\
Definition:

```solidity
function renewHandleOf(uint256 _projectId)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.RENEW_HANDLE) { ... }
```

* `_projectId` Is the ID of the project to which the handle being renewed belongs.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.RENEW_HANDLE` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.



1.  Get a reference to the project's current handle.



    _Internal references:_

    * [`handleOf`](../properties/handleof.md)

    ```javascript
    // Get the handle of the project.
    bytes32 _handle = handleOf[_projectId];
    ```


2.  Remove the `challengeExpiryOf` the `_handle`. Anyone will be able to reissue a challenge through the [`challengeHandle`](challengehandle.md) transaction, and await the challenge period from that time.



    _Internal references:_

    * [`challengeExpiryOf`](../properties/challengeexpiryof.md)

    ```javascript
    // Reset the challenge to 0.
    challengeExpiryOf[_handle] = 0;
    ```


3.  Emit a `RenewHandle` event with the all relevant parameters.



    _Event references:_

    * [`RenewHandle`](../events/renewhandle.md)

    ```javascript
    emit RenewHandle(_handle, _projectId, msg.sender);
    ```
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice
  Allows a project to renew its handle, which cancels any pending challenges.

  @dev 
  Only a project's owner or operator can renew its handle.

  @param _projectId The ID of the project to which the handle being renewed belongs. 
*/
function renewHandleOf(uint256 _projectId)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.RENEW_HANDLE)
{
  // Get the handle of the project.
  bytes32 _handle = handleOf[_projectId];

  // Reset the challenge to 0.
  challengeExpiryOf[_handle] = 0;

  emit RenewHandle(_handle, _projectId, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name              | Data                                                                                                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`RenewHandle`** | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>address caller</code></li></ul><p><a href="../events/renewhandle.md">more</a></p> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

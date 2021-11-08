# challengeHandle

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows anyone to challenge a project's handle.**

**After one year, the handle can be claimed by anyone if the challenge isn't answered by the handle's project.**

**This can be used to make sure a handle belonging to a stale project isn't lost forever.**

## Definition

```solidity
function challengeHandle(bytes32 _handle) external override { ... }
```

* Arguments:
  * `_handle` is the handle being challenged.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.

## Body

1.  Get a reference to the project to which the handle being challenged belongs.

    ```solidity
    // Get a reference to the ID of the project to which the handle belongs.
    uint256 _projectId = idFor[_handle];
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
2.  Check if the handle is being used.

    ```solidity
    // No need to challenge a handle that's not taken.
    require(_projectId > 0, '0x0d: HANDLE_NOT_TAKEN');
    ```
3.  Check if the handle is already being challenged.

    ```solidity
    // No need to challenge again if a handle is already being challenged.
    require(challengeExpiryOf[_handle] == 0, '0x0e: CHALLENGE_OPEN');
    ```

    _Internal references:_

    * [`challengeExpiryOf`](../properties/challengeexpiryof.md)
4.  The challenge will expire one year from the current timestamp. If the `_handle` is not renewed before then, anyone will be able to claim the handle by calling [`claimHandle`](claimhandle.md).

    ```solidity
    // The challenge will expire in a year, at which point the handle can be claimed if it has yet to be renewed.
    uint256 _challengeExpiry = block.timestamp + _SECONDS_IN_YEAR;
    ```

    _Internal references:_

    * [`_SECONDS_IN_YEAR`](../properties/_seconds\_in\_year.md)
5.  Store the `_challengeExpiry` as the `challengeExpiryOf` the provided `_handle`.

    ```solidity
    // Store the challenge expiry for the handle.
    challengeExpiryOf[_handle] = _challengeExpiry;
    ```

    _Internal references:_

    * [`challengeExpiryOf`](../properties/challengeexpiryof.md)
6.  Emit a `ChallengeHandle` event with the relevant parameters.

    ```solidity
    emit ChallengeHandle(_handle, _projectId, _challengeExpiry, msg.sender);
    ```

    _Event references:_

    * [`ChallengeHandle`](../events/challengehandle.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Allows anyone to challenge a project's handle. After one year, the handle can be claimed by anyone if the challenge isn't answered by the handle's project.
  This can be used to make sure a handle belonging to a stale project isn't lost forever.

  @param _handle The handle to challenge.
*/
function challengeHandle(bytes32 _handle) external override {
  // Get a reference to the ID of the project to which the handle belongs.
  uint256 _projectId = idFor[_handle];

  // No need to challenge a handle that's not taken.
  require(_projectId > 0, '0x0d: HANDLE_NOT_TAKEN');

  // No need to challenge again if a handle is already being challenged.
  require(challengeExpiryOf[_handle] == 0, '0x0e: CHALLENGE_OPEN');

  // The challenge will expire in a year, at which point the handle can be claimed if it has yet to be renewed.
  uint256 _challengeExpiry = block.timestamp + _SECONDS_IN_YEAR;

  // Store the challenge expiry for the handle.
  challengeExpiryOf[_handle] = _challengeExpiry;

  emit ChallengeHandle(_handle, _projectId, _challengeExpiry, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                       | Description                                       |
| ---------------------------- | ------------------------------------------------- |
| **`0x0d: HANDLE_NOT_TAKEN`** | Thrown if the handle isn't yet taken.             |
| **`0x0e: CHALLENGE_OPEN`**   | Thrown if the handle is already being challenged. |
{% endtab %}

{% tab title="Events" %}
| Name                                                  | Data                                                                                                                                                                                  |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ChallengeHandle`**](../events/challengehandle.md) | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 challengeExpiry</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

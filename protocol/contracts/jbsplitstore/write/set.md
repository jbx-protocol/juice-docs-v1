# set

Contract: [`JBSplitStore`](../)​‌

Interface: [`IJBSplitStore`](../../../interfaces/ijbsplitstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Sets a project's splits.**

_Only the owner or operator of a project, or the current controller contract of the project, can set its splits._

_The new splits must include any currently set splits that are locked._

# Definition

```solidity
function set(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group,
  JBSplit[] memory _splits
)
  external
  override
  requirePermissionAllowingOverride(
      projects.ownerOf(_projectId),
      _projectId,
      JBOperations.SET_SPLITS,
      address(directory.controllerOf(_projectId)) == msg.sender
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project for which splits are being added.
  * `_domain` is an identifier within which the splits should be considered active.
  * `_group` is an identifier between of splits being set. All splits within this `_group` must add up to within 100%.
  * `_splits` are the [`JBSplit`](../../../data-structures/jbsplit.md)s to set.
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.SET_SPLITS` permission by the project owner for the provided `_projectId` , or from the current controller of the `_projectId` of the specified.
* The function overrides a function definition from the `IJBSplitsStore` interface.
* The function doesn't return anything.

# Body

1.  Get a reference to the current splits set for the specified `_projectId`'s `_domain`, within the specified `_group`.

    ```solidity
    // Get a reference to the project's current splits.
    JBSplit[] memory _currentSplits = _splitsOf[_projectId][_domain][_group];
    ```

    _Internal references:_

    * [`_splitsOf`](../properties/\_splitsof.md)
2.  Loop through each `_currentSplits` to make sure the new `_splits` being set respect any current split bound by a lock constraint.

    ```solidity
    // Check to see if all locked splits are included.
    for (uint256 _i = 0; _i < _currentSplits.length; _i++) { ... }
    ```
3.  If the current split isn't locked, move on to the next one.

    ```solidity
    if (block.timestamp >= _currentSplits[_i].lockedUntil) continue;
    ```
4. If the current split is locked, check to make sure the new `_splits` includes it. The only property of a locked split that can have changed is its `lockedUntil` property, which can be extended.

```solidity
// Keep a reference to whether or not the locked split being iterated on is included.
bool _includesLocked = false;

for (uint256 _j = 0; _j < _splits.length; _j++) {
  // Check for sameness.
  if (
    _splits[_j].percent == _currentSplits[_i].percent &&
    _splits[_j].beneficiary == _currentSplits[_i].beneficiary &&
    _splits[_j].allocator == _currentSplits[_i].allocator &&
    _splits[_j].projectId == _currentSplits[_i].projectId &&
    // Allow lock extention.
    _splits[_j].lockedUntil >= _currentSplits[_i].lockedUntil
  ) _includesLocked = true;
}
```

Check to make sure the provided `_splits` includes any locked current splits.

````
```solidity
require(_includesLocked, '0x0f: SOME_LOCKED');
```
````

5\. After the loop, delete the current splits from storage so we can repopulate them.

```solidity
// Delete from storage so splits can be repopulated.
delete _splitsOf[_projectId][_domain][_group];
```

Internal references:

* [`_splitsOf`](../properties/\_splitsof.md)
*   Store a local variable to keep track of all the percents from the splits.

    ```solidity
    // Add up all the percents to make sure they cumulative are under 100%.
    uint256 _percentTotal = 0;
    ```
*   Loop through each newly provided `_splits` to validate

    ```solidity
    for (uint256 _i = 0; _i < _splits.length; _i++) { ... }
    ```
*   Check that the percent for the current split is not zero.

    ```solidity
    // The percent should be greater than 0.
    require(_splits[_i].percent > 0, '0x10: BAD_SPLIT_PERCENT');
    ```
*   Check that the split specifies a recipient. Either an `allocator` must be specified or a `beneficiary` must be specified.

    ```solidity
    // The allocator and the beneficiary shouldn't both be the zero address.
    require(
      _splits[_i].allocator != IJBSplitAllocator(address(0)) ||
        _splits[_i].beneficiary != address(0),
      '0x11: ZERO_ADDRESS'
    );
    ```
*   Increment the total percents that have been accumulated so far.

    ```solidity
    // Add to the total percents.
    _percentTotal = _percentTotal + _splits[_i].percent;
    ```
*   Make sure the accumulated percents are under 100%. Split percents are out of 10000.

    ```solidity
    // The total percent should be less than 10000.
    require(_percentTotal <= 10000, '0x12: BAD_TOTAL_PERCENT');
    ```
*   Push the split onto the stored `_splits` value.

    ```solidity
    // Push the new split into the project's list of splits.
    _splitsOf[_projectId][_domain][_group].push(_splits[_i]);
    ```

    Internal references:

    * [`_splitsOf`](../properties/\_splitsof.md)
*   For each added split, emit a `SetSplit` event with all relevant parameters.

    ```solidity
    emit SetSplit(_projectId, _domain, _group, _splits[_i], msg.sender);
    ```

    _Event references:_

    * [`SetSplit`](../events/setsplit.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Sets a project's splits.

  @dev
  Only the owner or operator of a project, or the current controller contract of the project, can set its splits.

  @dev
  The new splits must include any currently set splits that are locked.

  @param _projectId The ID of the project for which splits are being added.
  @param _domain An identifier within which the splits should be considered active.
  @param _group An identifier between of splits being set. All splits within this _group must add up to within 100%.
  @param _splits The splits to set.
*/
function set(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group,
  JBSplit[] memory _splits
)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_SPLITS,
    address(directory.controllerOf(_projectId)) == msg.sender
  )
{
  // Get a reference to the project's current splits.
  JBSplit[] memory _currentSplits = _splitsOf[_projectId][_domain][_group];

  // Check to see if all locked splits are included.
  for (uint256 _i = 0; _i < _currentSplits.length; _i++) {
    // If not locked, continue.
    if (block.timestamp >= _currentSplits[_i].lockedUntil) continue;

    // Keep a reference to whether or not the locked split being iterated on is included.
    bool _includesLocked = false;

    for (uint256 _j = 0; _j < _splits.length; _j++) {
      // Check for sameness.
      if (
        _splits[_j].percent == _currentSplits[_i].percent &&
        _splits[_j].beneficiary == _currentSplits[_i].beneficiary &&
        _splits[_j].allocator == _currentSplits[_i].allocator &&
        _splits[_j].projectId == _currentSplits[_i].projectId &&
        // Allow lock extention.
        _splits[_j].lockedUntil >= _currentSplits[_i].lockedUntil
      ) _includesLocked = true;
    }
    require(_includesLocked, '0x0f: SOME_LOCKED');
  }

  // Delete from storage so splits can be repopulated.
  delete _splitsOf[_projectId][_domain][_group];

  // Add up all the percents to make sure they cumulative are under 100%.
  uint256 _percentTotal = 0;

  for (uint256 _i = 0; _i < _splits.length; _i++) {
    // The percent should be greater than 0.
    require(_splits[_i].percent > 0, '0x10: BAD_SPLIT_PERCENT');

    // The allocator and the beneficiary shouldn't both be the zero address.
    require(
      _splits[_i].allocator != IJBSplitAllocator(address(0)) ||
        _splits[_i].beneficiary != address(0),
      '0x11: ZERO_ADDRESS'
    );

    // Add to the total percents.
    _percentTotal = _percentTotal + _splits[_i].percent;

    // The total percent should be less than 10000.
    require(_percentTotal <= 10000, '0x12: BAD_TOTAL_PERCENT');

    // Push the new split into the project's list of splits.
    _splitsOf[_projectId][_domain][_group].push(_splits[_i]);

    emit SetSplit(_projectId, _domain, _group, _splits[_i], msg.sender);
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                        | Description                                                                   |
| ----------------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`**       | Thrown if the splits that are being set override some splits that are locked. |
| **`0x10: BAD_SPLIT_PERCENT`** | Thrown if the split has specified a percent of 0.                             |
| **`0x11: ZERO_ADDRESS`**      | Thrown if the split doesn't specify a destination.                            |
| **`0x12: BAD_TOTAL_PERCENT`** | Thrown if the split percents add up more than 100%.                           |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                 |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](../events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

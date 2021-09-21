# set

Contract:[`JBSplitStore`](../)​‌

Interface: `IJBSplitStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets a project's splits.**

_Only the owner or operator of a project, or the current controller contract of the project, can set its splits._

_The new splits must include any currently set splits that are locked._  
  
Definition:

```javascript
function set(
    uint256 _projectId,
    uint256 _domain,
    uint256 _group,
    Split[] memory _splits
)
    external
    override
    requirePermissionAllowingOverride(
        projects.ownerOf(_projectId),
        _projectId,
        JBOperations.SetSplits,
        directory.controllerOf(_projectId) == msg.sender
    ) { ... }
```

* `_projectId` is the ID of the project for which splits are being added.
* `_domain` is an identifier within which the splits should be considered active.
* `_group` is an identifier between of splits being set. All splits within this `_group` must add up to within 100%.
* `_splits` are the splits to set.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermissionacceptingalternateaddress.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.SetSplits` permission by the project owner for the provided `_projectId` , or from the current controller of the `_projectId`for the specified.
* The function overrides a function definition from the `IJBSplitsStore` interface.
* The function doesn't return anything.

1. Get a reference to the current splits set for the specified `_projectId` 's `_domain`, within the specified `_group`.   


   _Internal references:_

   * [`_splitsOf`](../properties/_splitsof.md)

   ```javascript
   // Get a reference to the project's current splits.
   Split[] memory _currentSplits = _splitsOf[_projectId][_domain][_group];
   ```

2. Loop through each `_currentSplits` to make sure the new `_splits` being set respect any current split bound by a lock constraint.

   ```javascript
   // Check to see if all locked splits are included.
   for (uint256 _i = 0; _i < _currentSplits.length; _i++) { ... }
   ```

3. If the current split isn't locked, move on to the next one.

   ```javascript
   if (block.timestamp >= _currentSplits[_i].lockedUntil) continue;
   ```

4. If the current split is locked, check to make sure the new `_splits` includes it. The only property of a locked split that can have changed is its `lockedUntil` property, which can be extended.

   ```javascript
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

5. Check to make sure the provided `_splits` includes any locked current splits.

   ```javascript
   require(_includesLocked, '0x0f: SOME_LOCKED');
   ```

6. After the loop, delete the current splits from storage so we can repopulate them.  


   Internal references:

   * [`_splitsOf`](../properties/_splitsof.md)

   ```javascript
   // Delete from storage so splits can be repopulated.
   delete _splitsOf[_projectId][_domain][_group];
   ```

7. Store a local variable to keep track of all the percents from the splits.

   ```javascript
   // Add up all the percents to make sure they cumulative are under 100%.
   uint256 _percentTotal = 0;
   ```

8. Loop through each newly provided `_splits` to validate 

   ```javascript
   for (uint256 _i = 0; _i < _splits.length; _i++) { ... }
   ```

9. Check that the percent for the current split is not zero.

   ```javascript
   // The percent should be greater than 0.
   require(_splits[_i].percent > 0, '0x10: BAD_SPLIT_PERCENT');
   ```

10. Check that the split specifies a recipient. Either an `allocator` must be specified or a `beneficiary` must be specified.

    ```javascript
    // The allocator and the beneficiary shouldn't both be the zero address.
    require(
      _splits[_i].allocator != IJBSplitAllocator(address(0)) ||
        _splits[_i].beneficiary != address(0),
      '0x11: ZERO_ADDRESS'
    );
    ```

11. Increment the total percents that have been accumulated so far.

    ```javascript
    // Add to the total percents.
    _percentTotal = _percentTotal + _splits[_i].percent;
    ```

12. Make sure the accumulated percents are under 100%. Split percents are out of 10000.

    ```javascript
    // The total percent should be less than 10000.
    require(_percentTotal <= 10000, '0x12: BAD_TOTAL_PERCENT');
    ```

13. Push the split onto the stored `_splits` value.   


    Internal references:

    * [`_splitsOf`](../properties/_splitsof.md)

    ```javascript
    // Push the new split into the project's list of splits.
    _splitsOf[_projectId][_domain][_group].push(_splits[_i]);
    ```

14. For each added split, emit a `SetSplit` event with all relevant parameters.   


    _Event references:_

    * [`SetSplit`](../events/setsplit.md) 

    ```javascript
    emit SetSplit(_projectId, _domain, _group, _splits[_i], msg.sender);
    ```

  

  






  
{% endtab %}

{% tab title="Only code" %}
```javascript
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
  Split[] memory _splits
)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_SPLITS,
    directory.controllerOf(_projectId) == msg.sender
  )
{
  // Get a reference to the project's current splits.
  Split[] memory _currentSplits = _splitsOf[_projectId][_domain][_group];

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
| String | Description |
| :--- | :--- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
| **`0x10: BAD_SPLIT_PERCENT`** | Thrown if the split has specified a percent of 0. |
| **`0x11: ZERO_ADDRESS`** | Thrown if the split doesn't specify a destination. |
| **`0x12: BAD_TOTAL_PERCENT`** | Thrown if the split percents add up more than 100%. |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>SetSplit</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 indexed domain</code> 
          </li>
          <li><code>uint256 indexed group</code> 
          </li>
          <li><code>Split split</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../events/setsplit.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}


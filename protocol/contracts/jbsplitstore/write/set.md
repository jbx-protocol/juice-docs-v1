# set

Contract:[`JBSplitStore`](../)​‌

Interface: `IJBSplitStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets a project's splits.**

_Only the owner or operator of a project, or the current terminal of the project, can set its splits._

**The new splits must include any currently set splits that are locked.**

  
  
Definition:
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Sets a project's splits.

  @dev
  Only the owner or operator of a project, or the current terminal of the project, can set its splits.

  @dev
  The new splits must include any currently set splits that are locked.

  @param _projectId The ID of the project to add splits to.
  @param _configuration The funding cycle configuration to set the splits to be active during.
  @param _group The group of splits being set.
  @param _splits The splits to set.
*/
function set(
    uint256 _projectId,
    uint256 _configuration,
    uint256 _group,
    Split[] memory _splits
)
    external
    override
    requirePermissionAcceptingAlternateAddress(
        projects.ownerOf(_projectId),
        _projectId,
        JBOperations.SetSplits,
        address(directory.terminalOf(_projectId))
    )
{
    // Get a reference to the project's current splits.
    Split[] memory _currentSplits = _splitsOf[_projectId][_configuration][
        _group
    ];

    // Check to see if all locked splits are included.
    for (uint256 _i = 0; _i < _currentSplits.length; _i++) {
        if (block.timestamp < _currentSplits[_i].lockedUntil) {
            bool _includesLocked = false;
            for (uint256 _j = 0; _j < _splits.length; _j++) {
                // Check for sameness.
                if (
                    _splits[_j].percent == _currentSplits[_i].percent &&
                    _splits[_j].beneficiary ==
                    _currentSplits[_i].beneficiary &&
                    _splits[_j].allocator == _currentSplits[_i].allocator &&
                    _splits[_j].projectId == _currentSplits[_i].projectId &&
                    // Allow lock extention.
                    _splits[_j].lockedUntil >=
                    _currentSplits[_i].lockedUntil
                ) _includesLocked = true;
            }
            require(_includesLocked, "JBSplitsStore::set: SOME_LOCKED");
        }
    }

    // Delete from storage so splits can be repopulated.
    delete _splitsOf[_projectId][_configuration][_group];

    // Add up all the percents to make sure they cumulative are under 100%.
    uint256 _percentTotal = 0;

    for (uint256 _i = 0; _i < _splits.length; _i++) {
        // The percent should be greater than 0.
        require(
            _splits[_i].percent > 0,
            "JBSplitsStore::set: BAD_SPLIT_PERCENT"
        );

        // The allocator and the beneficiary shouldn't both be the zero address.
        require(
            _splits[_i].allocator != IJBSplitAllocator(address(0)) ||
                _splits[_i].beneficiary != address(0),
            "JBSplitsStore::set: ZERO_ADDRESS"
        );

        // Add to the total percents.
        _percentTotal = _percentTotal + _splits[_i].percent;

        // The total percent should be less than 10000.
        require(
            _percentTotal <= 10000,
            "JBSplitsStore::set: BAD_TOTAL_PERCENT"
        );
        
        // Push the new split into the project's list of splits.
        _splitsOf[_projectId][_configuration][_group].push(_splits[_i]);

        emit SetSplit(
            _projectId,
            _configuration,
            _group,
            _splits[_i],
            msg.sender
        );
    }
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBSplitStore::set: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
| **`JBSplitStore::set: BAD_SPLIT_PERCENT`** | Thrown if the split has specified a percent of 0. |
| **`JBSplitStore::set: ZERO_ADDRESS`** | Thrown if the split doesn't specify a destination. |
| **`JBSplitStore::set: BAD_TOTAL_PERCENT`** | Thrown if the split percents add up more than 100%. |
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
          <li><code>uint256 indexed configuration</code> 
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


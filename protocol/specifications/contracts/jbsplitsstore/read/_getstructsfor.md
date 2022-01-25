# \_getStructsFor

Contract: [`JBSplitsStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Unpack splits' packed stored values into easy-to-work-with spit structs.**

## Definition

```solidity
function _getStructsFor(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group
) private view returns (JBSplit[] memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get splits for.
  * `_domain` is an identifier within which the returned splits should be considered active.
  * `_group` is the identifying group of the splits.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns an array of [`JBSplit`](../../../data-structures/jbsplit.md)s.

## Body

1.  Get a reference to the expected number of splits for the specified `_domain` and `_group`.

    ```solidity
    // Get a reference to the number of splits that need to be added to the returned array.
    uint256 _splitCount = _splitCountOf[_projectId][_domain][_group];
    ```

    Internal references:

    * [`_splitCountOf`](../properties/\_splitcountof.md)

2.  Inititalize an array with length equal to the number of splits expected.

    ```solidity
    // Initialize an array to be returned that has the set length.
    JBSplit[] memory _splits = new JBSplit[](_splitCount);
    ```

3.  For each index, parse out the packed split parts into [`JBSplit`](../../../data-structures/jbsplit.md) structs and add to the array. The packed splits are stored in two different `uint256` slots, the second of which contains info that is populated far less frequently.

    ```solidity
    // Loop through each split and unpack the values into structs.
    for (uint256 _i = 0; _i < _splitCount; _i++) {
      // Get a reference to the fist packed data.
      uint256 _packedSplitPart1 = _packedSplitParts1Of[_projectId][_domain][_group][_i];

      JBSplit memory _split;

      _split.preferClaimed = (_packedSplitPart1 & 1) == 1;
      _split.percent = uint256(uint32(_packedSplitPart1 >> 1));
      _split.projectId = uint256(uint56(_packedSplitPart1 >> 33));
      _split.beneficiary = payable(address(uint160(_packedSplitPart1 >> 89)));

      // Get a reference to the second packed data.
      uint256 _packedSplitPart2 = _packedSplitParts2Of[_projectId][_domain][_group][_i];

      // If there's anything in it, unpack.
      if (_packedSplitPart2 > 0) {
        _split.lockedUntil = uint256(uint48(_packedSplitPart2));
        _split.allocator = IJBSplitAllocator(address(uint160(_packedSplitPart2 >> 48)));
      }

      // Add the split to the value being returned.
      _splits[_i] = _split;
    }
    ```

    Internal references:

    * [`_packedSplitParts1Of`](../properties/\_packedsplitparts1of.md)
    * [`_packedSplitParts2Of`](../properties/\_packedsplitparts2of.md)

4.  Return the array of splits.

    ```solidity
    return _splits;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Unpack splits' packed stored values into easy-to-work-with spit structs.

  @param _projectId The ID of the project to which the split belongs.
  @param _domain The identifier within which the returned splits should be considered active.
  @param _group The identifying group of the splits.

  @return splits The split structs.
*/
function _getStructsFor(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group
) private view returns (JBSplit[] memory) {
  // Get a reference to the number of splits that need to be added to the returned array.
  uint256 _splitCount = _splitCountOf[_projectId][_domain][_group];

  // Initialize an array to be returned that has the set length.
  JBSplit[] memory _splits = new JBSplit[](_splitCount);

  // Loop through each split and unpack the values into structs.
  for (uint256 _i = 0; _i < _splitCount; _i++) {
    // Get a reference to the fist packed data.
    uint256 _packedSplitPart1 = _packedSplitParts1Of[_projectId][_domain][_group][_i];

    JBSplit memory _split;

    _split.preferClaimed = (_packedSplitPart1 & 1) == 1;
    _split.percent = uint256(uint32(_packedSplitPart1 >> 1));
    _split.projectId = uint256(uint56(_packedSplitPart1 >> 33));
    _split.beneficiary = payable(address(uint160(_packedSplitPart1 >> 89)));

    // Get a reference to the second packed data.
    uint256 _packedSplitPart2 = _packedSplitParts2Of[_projectId][_domain][_group][_i];

    // If there's anything in it, unpack.
    if (_packedSplitPart2 > 0) {
      _split.lockedUntil = uint256(uint48(_packedSplitPart2));
      _split.allocator = IJBSplitAllocator(address(uint160(_packedSplitPart2 >> 48)));
    }

    // Add the split to the value being returned.
    _splits[_i] = _split;
  }

  return _splits;
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

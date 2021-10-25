# _distributeToReservedTokenSplitsOf

{% tabs %}
{% tab title="Step by step" %}
**Distributed tokens to the splits according to the specified funding cycle configuration.**

# Definition

```solidity
function _distributeToReservedTokenSplitsOf(JBFundingCycle memory _fundingCycle, uint256 _amount)
  private
  returns (uint256 leftoverAmount) { ... }
```

* Arguments:
  * `_fundingCycle` is the funding cycle to base the token distribution on.
  * `_amount` is the total amount of tokens to mint.
* The function is private to this contract.
* The function returns the leftover amount after all splits have been distributed.

# Body

TODO
{% endtab %}

{% tab title="Only code" %}
```solidity
/**
  @notice
  Distributed tokens to the splits according to the specified funding cycle configuration.

  @param _fundingCycle The funding cycle to base the token distribution on.
  @param _amount The total amount of tokens to mint.

  @return leftoverAmount If the splits percents dont add up to 100%, the leftover amount is returned.
*/
function _distributeToReservedTokenSplitsOf(JBFundingCycle memory _fundingCycle, uint256 _amount)
  private
  returns (uint256 leftoverAmount)
{
  // Set the leftover amount to the initial amount.
  leftoverAmount = _amount;

  // Get a reference to the project's reserved token splits.
  JBSplit[] memory _splits = splitsStore.splitsOf(
    _fundingCycle.projectId,
    _fundingCycle.configured,
    JBSplitsGroups.RESERVED_TOKENS
  );

  //Transfer between all splits.
  for (uint256 _i = 0; _i < _splits.length; _i++) {
    // Get a reference to the split being iterated on.
    JBSplit memory _split = _splits[_i];

    // The amount to send towards the split. JBSplit percents are out of 10000.
    uint256 _tokenCount = PRBMath.mulDiv(_amount, _split.percent, 10000);

    // Mints tokens for the split if needed.
    if (_tokenCount > 0)
      tokenStore.mintFor(
        // If a projectId is set in the split, set the project's owner as the beneficiary.
        // Otherwise use the split's beneficiary.
        _split.projectId != 0 ? projects.ownerOf(_split.projectId) : _split.beneficiary,
        _fundingCycle.projectId,
        _tokenCount,
        _split.preferUnstaked
      );

    // If there's an allocator set, trigger its `allocate` function.
    if (_split.allocator != IJBSplitAllocator(address(0)))
      _split.allocator.allocate(
        _tokenCount,
        2,
        _fundingCycle.projectId,
        _split.projectId,
        _split.beneficiary,
        _split.preferUnstaked
      );

    // Subtract from the amount to be sent to the beneficiary.
    leftoverAmount = leftoverAmount - _tokenCount;

    emit DistributeToReservedTokenSplit(
      _fundingCycle.id,
      _fundingCycle.projectId,
      _split,
      _tokenCount,
      msg.sender
    );
  }
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

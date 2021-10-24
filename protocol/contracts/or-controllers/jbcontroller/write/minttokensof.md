# mintTokensOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

{% tabs %}
{% tab title="Step by step" %}
**Sets a project's splits.**

_Only the owner or operator of a project, or the current controller contract of the project, can set its splits._

_The new splits must include any currently set splits that are locked._

## Definition

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

## Body
TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Mint new token supply into an account.

  @dev
  Only a project's owner or a designated operator can mint it.

  @param _projectId The ID of the project to which the tokens being burned belong.
  @param _tokenCount The amount of tokens to mint.
  @param _beneficiary The account that the tokens are being minted for.
  @param _memo A memo to pass along to the emitted event.
  @param _preferClaimedTokens Whether ERC20's should be burned first if they have been issued.
*/
function mintTokensOf(
  uint256 _projectId,
  uint256 _tokenCount,
  address _beneficiary,
  string calldata _memo,
  bool _preferClaimedTokens,
  bool _shouldReserveTokens
)
  external
  override
  nonReentrant
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.MINT,
    directory.isTerminalDelegateOf(_projectId, msg.sender)
  )
{
  // Can't send to the zero address.
  require(_beneficiary != address(0), 'ZERO_ADDRESS');

  // There should be tokens to mint.
  require(_tokenCount > 0, 'NO_OP');

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
  require(
    !_fundingCycle.mintPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
    'PAUSED'
  );

  if (_shouldReserveTokens && _fundingCycle.reservedRate() == 200) {
    // Subtract the total weighted amount from the tracker so the full reserved token amount can be printed later.
    _processedTokenTrackerOf[_projectId] =
      _processedTokenTrackerOf[_projectId] -
      int256(_tokenCount);
  } else {
    // Redeem the tokens, which burns them.
    tokenStore.mintFor(_beneficiary, _projectId, _tokenCount, _preferClaimedTokens);

    if (!_shouldReserveTokens)
      // Set the minted tokens as processed so that reserved tokens cant be minted against them.
      _processedTokenTrackerOf[_projectId] =
        _processedTokenTrackerOf[_projectId] +
        int256(_tokenCount);
  }

  emit MintTokens(_beneficiary, _projectId, _tokenCount, _memo, _shouldReserveTokens, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                  | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
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

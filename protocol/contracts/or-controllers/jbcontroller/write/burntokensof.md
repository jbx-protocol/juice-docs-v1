# burnTokensOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

{% tabs %}
{% tab title="Step by step" %}
TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Burns a token holder's supply.

  @dev
  Only a token's holder or a designated operator can burn it.

  @param _holder The account that is having its tokens burned.
  @param _projectId The ID of the project to which the tokens being burned belong.
  @param _tokenCount The number of tokens to burn.
  @param _memo A memo to pass along to the emitted event.
  @param _preferClaimedTokens Whether ERC20's should be burned first if they have been issued.
*/
function burnTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  string calldata _memo,
  bool _preferClaimedTokens
)
  external
  override
  nonReentrant
  requirePermissionAllowingOverride(
    _holder,
    _projectId,
    JBOperations.BURN,
    directory.isTerminalDelegateOf(_projectId, msg.sender)
  )
{
  // There should be tokens to burn
  require(_tokenCount > 0, 'NO_OP');

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
  require(
    !_fundingCycle.burnPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
    'PAUSED'
  );

  // Update the token tracker so that reserved tokens will still be correctly mintable.
  _subtractFromTokenTrackerOf(_projectId, _tokenCount);

  // Burn the tokens.
  tokenStore.burnFrom(_holder, _projectId, _tokenCount, _preferClaimedTokens);

  emit BurnTokens(_holder, _projectId, _tokenCount, _memo, msg.sender);
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

# burnTokensOf

Contract:[`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Burns a token holder's supply.**

_Only a token's holder, a designated operator, or a project's terminal's delegate can burn it._

# Definition

```solidity
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
  ) { ... }
```

* Arguments:
  * `_holder` is the account that is having its tokens burned.
  * `_projectId` is the ID of the project to which the tokens being burned belong.
  * `_tokenCount` is the number of tokens to burn.
  * `_memo` is a memo to pass along to the emitted event.
  * `_preferClaimedTokens` is a flag indicating whether ERC20's should be burned first if they have been issued.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.BURN` permission by the project owner for the provided `_projectId`, or from the one of the project's terminal's delegates..
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function doesn't return anything.

# Body

1.  Make sure there is a specified number of tokens to burn.

    ```solidity
    // There should be tokens to burn
    require(_tokenCount > 0, '0x32: NO_OP');
    ```

2.  Get a reference to the current funding cycle for the project.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)

3.  Make sure the current funding cycle for the project hasn't paused burning if the request is not coming from one of the project's terminals. If the request is coming from a terminal, allow burning  regardless of the pause state because it could be a sub-routine of another operation such as redemption.

    ```solidity
    // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
    require(
      !_fundingCycle.burnPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
      '0x33: PAUSED'
    );
    ```

    _External references:_

    * [`isTerminalDelegateOf`](../../../jbdirectory/read/isterminaldelegateof.md)

4.  Update the token tracker so that the correct amount of reserved tokens are still mintable after the burn.

    ```solidity
    // Update the token tracker so that reserved tokens will still be correctly mintable.
    _processedTokenTrackerOf[_projectId] =
      _processedTokenTrackerOf[_projectId] -
      int256(_tokenCount);
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../write/_processedtokentrackerof.md)

5.  Burn the tokens.

    ```solidity
    // Burn the tokens.
    tokenStore.burnFrom(_holder, _projectId, _tokenCount, _preferClaimedTokens);
    ```

    _External references:_

    * [`burnFrom`](../../../jbtokenstore/write/burnfrom.md)

6.  Emit a `BurnTokens` event with the all relevant parameters.

    ```solidity
    emit BurnTokens(_holder, _projectId, _tokenCount, _memo, msg.sender);
    ```

    _Event references:_

    * [`BurnTokens`](../events/burntokens.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Burns a token holder's supply.

  @dev
  Only a token's holder, a designated operator, or a project's terminal's delegate can burn it.

  @param _holder The account that is having its tokens burned.
  @param _projectId The ID of the project to which the tokens being burned belong.
  @param _tokenCount The number of tokens to burn.
  @param _memo A memo to pass along to the emitted event.
  @param _preferClaimedTokens A flag indicating whether ERC20's should be burned first if they have been issued.
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
  require(_tokenCount > 0, '0x32: NO_OP');

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
  require(
    !_fundingCycle.burnPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
    '0x33: PAUSED'
  );

  // Update the token tracker so that reserved tokens will still be correctly mintable.
  _processedTokenTrackerOf[_projectId] =
    _processedTokenTrackerOf[_projectId] -
    int256(_tokenCount);

  // Burn the tokens.
  tokenStore.burnFrom(_holder, _projectId, _tokenCount, _preferClaimedTokens);

  emit BurnTokens(_holder, _projectId, _tokenCount, _memo, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String             | Description                                                                                                                |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| **`0x32: NO_OP`**  | Thrown if no tokens are being burned.                                                                                      |
| **`0x33: PAUSED`** | Thrown if the request is not being made by a payment terminal, and the project's current funding cycle has paused burning. |
{% endtab %}

{% tab title="Events" %}
| Name                                        | Data                                                                                                                                                                                                                                                       |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`BurnTokens`**](../events/burntokens.md) | <p>ul></p><ul><li><code>address indexed holder</code></li></ul><ul><li><code>uint256 indexed projectId</code></li></ul><ul><li><code>uint256 count</code></li></ul><ul><li><code>string memo</code></li></ul><ul><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

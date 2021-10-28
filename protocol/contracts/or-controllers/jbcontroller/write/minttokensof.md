# mintTokensOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Mint new token supply into an account.**

_Only a project's owner, a designated operator, or one of its terminal's delegate can mint its tokens._

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
  * `_projectId` is the ID of the project to which the tokens being burned belong.
  * `_tokenCount` is the amount of tokens to mint.
  * `_beneficiary` is the account that the tokens are being minted for.
  * `_memo` is a memo to pass along to the emitted event.
  * `_preferClaimedTokens` is a flag indicating whether ERC20's should be burned first if they have been issued.
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.MINT` permission by the project owner for the provided `_projectId`, or from the one of the project's terminal's delegates.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function doesn't return anything.

# Body

1.  If the reserved rate isnt' 100%, make sure the provided beneficiary isn't the zero address.

    ```solidity
    // Can't send to the zero address.
    require(_reservedRate == 200 || _beneficiary != address(0), '0x2f: ZERO_ADDRESS');
    ```
2.  Make sure there is a specified number of tokens to mint.

    ```solidity
    // There should be tokens to mint.
    require(_tokenCount > 0, '0x30: NO_OP');
    ```
3.  Get a reference to the current funding cycle for the project.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
4.  Make sure the current funding cycle for the project hasn't paused minting if the request is not coming from one of the project's terminals. If the request is coming from a terminal, allow minting regardless of the pause state because it could be a sub-routine of another operation such as receiving payments.

    ```solidity
    // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
    require(
      !_fundingCycle.mintPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
      '0x31: PAUSED'
    );
    ```

    _External references:_

    * [`isTerminalDelegateOf`](../../../jbdirectory/read/isterminaldelegateof.md)
5.  If the operation should reserve 100% of the minted tokens, the token tracker should be updated to add a difference of the specified token count instead of minting the tokens directly. This will allow a future distribution of reserved tokens to mint the token count to reserved addresses. Otherwise, mint the tokens updating the token tracker if there is no intent to reserve tokens alongside the mint.

    ```solidity
    if (_reservedRate == 200) {
      // Subtract the total weighted amount from the tracker so the full reserved token amount can be printed later.
      _processedTokenTrackerOf[_projectId] =
        _processedTokenTrackerOf[_projectId] -
        int256(_tokenCount);
    } else {
      // The unreserved token count that will be minted for the beneficiary.
      beneficiaryTokenCount = PRBMath.mulDiv(_tokenCount, 200 - _reservedRate, 200);

      // Mint the tokens.
      tokenStore.mintFor(_beneficiary, _projectId, beneficiaryTokenCount, _preferClaimedTokens);

      if (_reservedRate == 0)
        // If there's no reserved rate, increment the tracker with the newly minted tokens.
        _processedTokenTrackerOf[_projectId] =
          _processedTokenTrackerOf[_projectId] +
          int256(beneficiaryTokenCount);
    }
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../read/\_processedtokentrackerof.md)

    _External references:_

    * [`mintFor`](../../../jbtokenstore/write/mintFor.md)
6.  Emit a `MintTokens` event with the relevant parameters.

    ```solidity
    emit MintTokens(
      _beneficiary,
      _projectId,
      _tokenCount,
      _memo,
      _shouldReserveTokens,
      _reservedRate,
      msg.sender
    );
    ```

    _Event references:_

    * [`MintTokens`](../events/minttokens.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Mint new token supply into an account.

  @dev
  Only a project's owner, a designated operator, or one of its terminal's delegate can mint its tokens.

  @param _projectId The ID of the project to which the tokens being burned belong.
  @param _tokenCount The amount of tokens to mint.
  @param _beneficiary The account that the tokens are being minted for.
  @param _memo A memo to pass along to the emitted event.
  @param _preferClaimedTokens A flag indicating whether ERC20's should be burned first if they have been issued.
  @param _reservedRate The reserved rate to use when minting tokens. A positive amount will reduce the token count minted to the beneficiary.

  @return beneficiaryTokenCount The amount of tokens minted for the beneficiary.
*/
function mintTokensOf(
  uint256 _projectId,
  uint256 _tokenCount,
  address _beneficiary,
  string calldata _memo,
  bool _preferClaimedTokens,
  uint256 _reservedRate
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
  returns (uint256 beneficiaryTokenCount)
{
  // Can't send to the zero address.
  require(_reservedRate == 200 || _beneficiary != address(0), '0x2f: ZERO_ADDRESS');

  // There should be tokens to mint.
  require(_tokenCount > 0, '0x30: NO_OP');

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // If the message sender is not a terminal delegate, the current funding cycle must not be paused.
  require(
    !_fundingCycle.mintPaused() || directory.isTerminalDelegateOf(_projectId, msg.sender),
    '0x31: PAUSED'
  );

  if (_reservedRate == 200) {
    // Subtract the total weighted amount from the tracker so the full reserved token amount can be printed later.
    _processedTokenTrackerOf[_projectId] =
      _processedTokenTrackerOf[_projectId] -
      int256(_tokenCount);
  } else {
    // The unreserved token count that will be minted for the beneficiary.
    beneficiaryTokenCount = PRBMath.mulDiv(_tokenCount, 200 - _reservedRate, 200);

    // Mint the tokens.
    tokenStore.mintFor(_beneficiary, _projectId, beneficiaryTokenCount, _preferClaimedTokens);

    if (_reservedRate == 0)
      // If there's no reserved rate, increment the tracker with the newly minted tokens.
      _processedTokenTrackerOf[_projectId] =
        _processedTokenTrackerOf[_projectId] +
        int256(beneficiaryTokenCount);
  }

  emit MintTokens(_beneficiary, _projectId, _tokenCount, _memo, _reservedRate, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                                                                                                |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| **`0x2f: ZERO_ADDRESS`** | Thrown if the token beneficiary is the zero address.                                                                       |
| **`0x30: NO_OP`**        | Thrown if no tokens are being minted.                                                                                      |
| **`0x31: PAUSED`**       | Thrown if the request is not being made by a payment terminal, and the project's current funding cycle has paused minting. |
{% endtab %}

{% tab title="Events" %}
| Name                                        | Data                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`MintTokens`**](../events/minttokens.md) | <p>&#x3C;</p><ul><li><code>address indexed beneficiary</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 count</code></li><li><code>string memo</code></li><li><code>bool shouldReserveTokens</code></li><li><code>uint256 reservedRate</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

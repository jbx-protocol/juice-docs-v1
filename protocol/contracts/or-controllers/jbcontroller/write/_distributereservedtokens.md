# _distributeReservedTokens

{% tabs %}
{% tab title="Step by step" %}
**Distributes all outstanding reserved tokens for a project.**

# Definition

```solidity
function _distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  private
  returns (uint256 count) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the reserved tokens belong.
  * `_memo` is a memo to pass along to the emitted event.
* The function is private to this contract.
* The function returns the amount of reserved tokens that were minted.

# Body

1.  Get a reference to the current funding cycle of the project.

    ```solidity
    // Get the current funding cycle to read the reserved rate from.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)

2.  Get a reference to the current total supply of tokens issued for the project.

    ```solidity
    // Get a reference to new total supply of tokens before minting reserved tokens.
    uint256 _totalTokens = tokenStore.totalSupplyOf(_projectId);
    ```

    _External references:_

    * [`totalSupplyOf`](../../../jbtokenstore/read/totalsupplyof.md)

3.  Get a reference to the current amount of reserved tokens given the current state of the tracker, the current funding cycle's reserved rate, and the current total token supply.

    ```solidity
    // Get a reference to the number of tokens that need to be minted.
    count = _reservedTokenAmountFrom(
      _processedTokenTrackerOf[_projectId],
      _fundingCycle.reservedRate(),
      _totalTokens
    );
    ```

    _Internal references:_

    * [`_reservedTokenAmountFrom`](../properties/_reservedtokenamountfrom.md)

4.  Set the tracker to be equal to the new current total token supply, which is the amount stored plus the amount that will be minted and distributed. 

    ```solidity
    // Set the tracker to be the new total supply.
    _processedTokenTrackerOf[_projectId] = int256(_totalTokens + count);
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../properties/_processedtokentrackerof.md)

5.  Get a reference to the project's owner.

    ```solidity
    // Get a reference to the project owner.
    address _owner = projects.ownerOf(_projectId);
    ```

    _Internal references:_

    * [`ownerOf`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721-ownerOf-uint256-)

6.  If there are outstanding reserved tokens, distribute them to reserved token splits. Get a reference to any leftover amount after the splits are settled.  

    ```solidity
    // Distribute tokens to splits and get a reference to the leftover amount to mint after all splits have gotten their share.
    uint256 _leftoverTokenCount = count == 0
      ? 0
      : _distributeToReservedTokenSplitsOf(_fundingCycle, count);
    ```

    _Internal references:_

    * [`_distributeToReservedTokenSplitsOf`](../properties/_distributetoreservedtokensplitsof.md)

7.  If there are any leftover reserved tokens, mint them for the project's owner.

    ```solidity
    // Mint any leftover tokens to the project owner.
    if (_leftoverTokenCount > 0) tokenStore.mintFor(_owner, _projectId, _leftoverTokenCount, false);
    ```

    _Internal references:_

    * [`mintFor`](../../../jbtokenstore/write/mintfor.md)

8.  Emit a `DistributeReservedTokens` event with the all relevant parameters.

    ```solidity
    emit DistributeReservedTokens(
      _fundingCycle.id,
      _projectId,
      _owner,
      count,
      _leftoverTokenCount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`DistributeReservedTokens`](../events/distributereservedtokens.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  See docs for `distributeReservedTokens`
*/
function _distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  private
  returns (uint256 count)
{
  // Get the current funding cycle to read the reserved rate from.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Get a reference to new total supply of tokens before minting reserved tokens.
  uint256 _totalTokens = tokenStore.totalSupplyOf(_projectId);

  // Get a reference to the number of tokens that need to be minted.
  count = _reservedTokenAmountFrom(
    _processedTokenTrackerOf[_projectId],
    _fundingCycle.reservedRate(),
    _totalTokens
  );

  // Set the tracker to be the new total supply.
  _processedTokenTrackerOf[_projectId] = int256(_totalTokens + count);

  // Get a reference to the project owner.
  address _owner = projects.ownerOf(_projectId);

  // Distribute tokens to splits and get a reference to the leftover amount to mint after all splits have gotten their share.
  uint256 _leftoverTokenCount = count == 0
    ? 0
    : _distributeToReservedTokenSplitsOf(_fundingCycle, count);

  // Mint any leftover tokens to the project owner.
  if (_leftoverTokenCount > 0) tokenStore.mintFor(_owner, _projectId, _leftoverTokenCount, false);

  emit DistributeReservedTokens(
    _fundingCycle.id,
    _projectId,
    _owner,
    count,
    _leftoverTokenCount,
    _memo,
    msg.sender
  );
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

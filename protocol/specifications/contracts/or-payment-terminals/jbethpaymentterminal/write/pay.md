# pay

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBTerminal`](../../../../interfaces/ijbterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Contribute ETH to a project.**

_The msg.value is the amount of the contribution in wei._

## Definition

```solidity
function pay(
  uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string calldata _memo,
  bytes calldata _delegateMetadata
) external payable override returns (uint256 fundingCycleId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being paid.
  * `_beneficiary` is the address to mint tokens for and pass along to the funding cycle's data source and delegate.
  * `_minReturnedTokens` is the minimum number of tokens expected in return.
  * `_preferClaimedTokens` is a flag indicating whether the request prefers to issue tokens unstaked rather than staked.
  * `_memo` is a memo to pass along to the emitted event, and passed along the the funding cycle's data source and delegate.
  * `_delegateMetadata` are bytes to send along to the delegate, if one is provided.
* The function can be accessed externally by anyone.
* The function accepts ETH.
* The resulting function overrides a function definition from the `IJBTerminal` interface.
* The function returns the id of the funding cycle that the payment was made during.

## Body

1.  Forward the call to the internal version of the function that is also used by other operations.

    ```solidity
    return _pay(
      msg.value,
      _projectId,
      _beneficiary,
      _minReturnedTokens,
      _preferClaimedTokens,
      _memo,
      _delegateMetadata
    );
    ```

    _Internal references:_

    * [`_pay`](\_pay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Contribute ETH to a project.

  @dev
  The msg.value is the amount of the contribution in wei.

  @param _projectId The ID of the project being paid.
  @param _beneficiary The address to mint tokens for and pass along to the funding cycle's data source and delegate.
  @param _minReturnedTokens The minimum number of tokens expected in return.
  @param _preferClaimedTokens A flag indicating whether the request prefers to issue tokens unstaked rather than staked.
  @param _memo A memo to pass along to the emitted event, and passed along the the funding cycle's data source and delegate.
  @param _delegateMetadata Bytes to send along to the delegate, if one is provided.

  @return The id of the funding cycle that the payment was made during.
*/
function pay(
  uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string calldata _memo,
  bytes calldata _delegateMetadata
) external payable override returns (uint256) {
  return _pay(
    msg.value,
    _projectId,
    _beneficiary,
    _minReturnedTokens,
    _preferClaimedTokens,
    _memo,
    _delegateMetadata
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

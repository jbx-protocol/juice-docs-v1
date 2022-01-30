# \_pay

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Contribute ETH to a project.**

### Definition

```solidity
function _pay(
  uint256 _amount,
  address _payer,
  uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string memory _memo,
  bytes memory _delegateMetadata
) private { ... }
```

* Arguments:
  * `_amount` is the amount being paid.
  * `_payer` is the address from which the payment is originating.
  * `_projectId` is the ID of the project being paid.
  * `_beneficiary` is the address to mint tokens for and pass along to the funding cycle's data source and delegate.
  * `_minReturnedTokens` is the minimum number of tokens expected in return.
  * `_preferClaimedTokens` is a flag indicating whether the request prefers to issue tokens unstaked rather than staked.
  * `_memo` is a memo to pass along to the emitted event, and passed along the the funding cycle's data source and delegate.
  * `_delegateMetadata` are bytes to send along to the delegate, if one is provided.
* The function is private to this contract.
* The function doesn't return anything.

### Body

1.  Make sure the provided beneficiary isn't the zero address.

    ```solidity
    // Cant send tokens to the zero address.
    if (_beneficiary == address(0)) {
      revert PAY_TO_ZERO_ADDRESS();
    }
    ```
2.  Create definitions for the funding cycle, weight, and token count. These will be later populated and used by the rest of the routine.

    ```solidity
    JBFundingCycle memory _fundingCycle;
    uint256 _weight;
    uint256 _tokenCount;
    ```
3.  Record the payment in the store. The return value will populate the previously defined variables. The `_preferClaimedTokens` and `_beneficiary` properties should be passed along packed into one `uint256`.

    ```solidity
    // Record the payment.
    (_fundingCycle, _weight, _tokenCount, _memo) = store.recordPaymentFrom(
      _payer,
      _amount,
      _projectId,
      (_preferClaimedTokens ? 1 : 0) | (uint256(uint160(_beneficiary)) << 1),
      _minReturnedTokens,
      _memo,
      _delegateMetadata
    );
    ```

    _External references:_

    * [`recordPaymentFrom`](../../jbethpaymentterminalstore/write/recordpaymentfrom.md)
4.  Emit a `Pay` event for the split being iterated on with the relevant parameters.

    ```solidity
    emit Pay(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _beneficiary,
      _fundingCycle,
      _amount,
      _weight,
      _tokenCount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`Pay`](../events/pay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  See the documentation for 'pay'.
*/
function _pay(
  uint256 _amount,
  address _payer,
  uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string memory _memo,
  bytes memory _delegateMetadata
) private {
  // Cant send tokens to the zero address.
  if (_beneficiary == address(0)) {
    revert PAY_TO_ZERO_ADDRESS();
  }

  JBFundingCycle memory _fundingCycle;
  uint256 _weight;
  uint256 _tokenCount;

  // Record the payment.
  (_fundingCycle, _weight, _tokenCount, _memo) = store.recordPaymentFrom(
    _payer,
    _amount,
    _projectId,
    (_preferClaimedTokens ? 1 : 0) | (uint256(uint160(_beneficiary)) << 1),
    _minReturnedTokens,
    _memo,
    _delegateMetadata
  );

  emit Pay(
    _fundingCycle.configuration,
    _fundingCycle.number,
    _projectId,
    _beneficiary,
    _amount,
    _weight,
    _tokenCount,
    _memo,
    msg.sender
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                    | Description                                            |
| ------------------------- | ------------------------------------------------------ |
| **`PAY_TO_ZERO_ADDRESS`** | Thrown if the provided benificary is the zero address. |
{% endtab %}

{% tab title="Events" %}
| Name                          | Data                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Pay`**](../events/pay.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed beneficiary</code></li><li><a href="../../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 amount</code></li><li><code>uint256 weight</code></li><li><code>uint256 tokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

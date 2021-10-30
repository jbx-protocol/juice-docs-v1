# addToBalanceOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBTerminal`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Receives and allocated funds belonging to the specified project.**

# Definition

```solidity
function addToBalanceOf(uint256 _projectId, string memory _memo) external payable override { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funds received belong.
  * `_memo` is a memo to pass along to the emitted event.
* The function can be accessed externally by anyone.
* The function accepts ETH.
* The resulting function overrides a function definition from the `IJBTerminal` interface.
* The function doesn't return anything.

# Body

1.  Make sure some ETH was sent along with the function call.

    ```solidity
    // Amount must be greater than 0.
    require(msg.value > 0, '0x4c: NO_OP');
    ```

2.  Record the added balance.

    ```solidity
    // Record the added funds in the data later.
    JBFundingCycle memory _fundingCycle = store.recordAddedBalanceFor(_projectId, msg.value);
    ```

    _External references:_

    * [`recordAddedBalanceFor`](../../jbethpaymentterminalstore/write/recordaddedbalancefor.md)

3.  Refund any held fees. This is useful to allow a project to withdraw funds from the protocol and subsequently add them back without paying eventually having to pay double fees. 

    ```solidity
    // Refund any held fees to make sure the project doesn't pay double for funds going in and out of the protocol.
    _refundHeldFees(_projectId, msg.value, _fundingCycle.fee);
    ```

    _Internal references:_

    * [`_refundHeldFees`](../_refundheldfees.md)

4.  Emit a `AddToBalance` event with the relevant parameters.

    ```solidity
    emit AddToBalance(_projectId, msg.value, _memo, msg.sender);
    ```

    _Event references:_

    * [`AddToBalance`](../events/addtobalance.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Receives and allocated funds belonging to the specified project.

  @param _projectId The ID of the project to which the funds received belong.
  @param _memo A memo to pass along to the emitted event.
*/
function addToBalanceOf(uint256 _projectId, string memory _memo) external payable override {
  // Amount must be greater than 0.
  require(msg.value > 0, '0x4c: NO_OP');

  // Record the added funds in the data later.
  JBFundingCycle memory _fundingCycle = store.recordAddedBalanceFor(_projectId, msg.value);

  // Refund any held fees to make sure the project doesn't pay double for funds going in and out of the protocol.
  _refundHeldFees(_projectId, msg.value, _fundingCycle.fee);

  emit AddToBalance(_projectId, msg.value, _memo, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String              | Description                                    |
| ------------------- | ---------------------------------------------- |
| **`0x4c: NO_OP`** | Thrown if the transaction had no value. |
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| [**`AddToBalance`**](../events/addtobalance.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 value</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}



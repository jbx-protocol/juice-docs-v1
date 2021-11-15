# heldFeesOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../interfaces/ijbethpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**The fees that are currently being held to be processed later for each project.**

## Definition

```solidity
function heldFeesOf(uint256 _projectId) external view override returns (JBFee[] memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the held fees belongs.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function returns an array of fees that are being held.

## Body

1.  This function just reads and returns the stored `_heldFeesOf` the project.

    ```solidity
    return _heldFeesOf[_projectId];
    ```

    _Internal references:_

    * [`_heldFeesOf`](../properties/\_heldfeesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The fees that are currently being held to be processed later for each project.

  @param _projectId The ID of the project for which fees are being held.

  @return An array of fees that are being held.
*/
function heldFeesOf(uint256 _projectId) external view override returns (JBFee[] memory) {
  return _heldFeesOf[_projectId];
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

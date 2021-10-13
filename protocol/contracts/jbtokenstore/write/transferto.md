# transferTo

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**Allows an unclaimed token holder to transfer them to another account, without claimed them to ERC-20s.**

_Only a token holder or an operator can transfer its unclaimed tokens_.

Definition:

```solidity
function transferTo(
  address _recipient,
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) { ... }
```

* Arguments:
  * `_recipient` is the recipient of the tokens.
  * `_holder` is the address to transfer tokens from.
  * `_projectId` is the ID of the project whose tokens are being transferred.
  * `_amount` is the amount of tokens to transfer.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.TRANSFER`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.



1. Make sure a recipient was specified. 

   ```solidity
   // Can't transfer to the zero address.
   require(_recipient != address(0), '0x26: ZERO_ADDRESS');
   ```


2. Make sure the holder of the tokens isn't also the recipient.

   ```solidity
   // An address can't transfer to itself.
   require(_holder != _recipient, '0x27: IDENTITY');
   ```


3. Make sure there is amount to transfer.
   
   ```solidity
   // There must be an amount to transfer.
   require(_amount > 0, '0x28: NO_OP');
   ```


4. Get a reference to the amount of unclaimed tokens the holder has for the project.

   ```solidity
   // Get a reference to the amount of unclaimed tokens.
   uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)


5. Make sure the holder has enough unclaimed tokens to transfer. 

   ```solidity
   // There must be enough unclaimed tokens to transfer.
   require(_amount <= _unclaimedBalance, '0x29: INSUFFICIENT_FUNDS');
   ```


6. Subtract the amount from the `unclaimedBalanceOf` the holder for the project. 

   ```solidity
   // Subtract from the holder.
   unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
  

6. Add the amount to the `unclaimedBalanceOf` the recipient for the project. 

   ```solidity
   // Add the tokens to the recipient.
   unclaimedBalanceOf[_recipient][_projectId] =
     unclaimedBalanceOf[_recipient][_projectId] +
     _amount;
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
   

7. Emit a `Transfer` event with the all relevant parameters.

   ```solidity
   emit Transfer(_holder, _projectId, _recipient, _amount, msg.sender);
   ```

   _Event references:_

   * [`Transfer`](../events/transfer.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Allows an unclaimed token holder to transfer them to another account, without claimed them to ERC-20s.

  @dev
  Only a token holder or an operator can transfer its unclaimed tokens.

  @param _recipient The recipient of the tokens.
  @param _holder The address to transfer tokens from.
  @param _projectId The ID of the project whose tokens are being transferred.
  @param _amount The amount of tokens to transfer.
*/
function transferTo(
  address _recipient,
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) {
  // Can't transfer to the zero address.
  require(_recipient != address(0), '0x26: ZERO_ADDRESS');

  // An address can't transfer to itself.
  require(_holder != _recipient, '0x27: IDENTITY');

  // There must be an amount to transfer.
  require(_amount > 0, '0x28: NO_OP');

  // Get a reference to the amount of unclaimed tokens.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // There must be enough unclaimed tokens to transfer.
  require(_amount <= _unclaimedBalance, '0x29: INSUFFICIENT_FUNDS');

  // Subtract from the holder.
  unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;

  // Add the tokens to the recipient.
  unclaimedBalanceOf[_recipient][_projectId] =
    unclaimedBalanceOf[_recipient][_projectId] +
    _amount;

  emit Transfer(_holder, _projectId, _recipient, _amount, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`0x26: ZERO_ADDRESS`** | Thrown if no recipient was speicified. |
| **`0x27: IDENTITY`** | Thrown if the holder is the same address as the recipient. |
| **`0x28: NO_OP`** | Thrown if no amount was specified to transfer. |
| **`0x29: INSUFFICIENT_FUNDS`** | Thrown if the holder doesn't have enough tokens to transfer. |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>Transfer</code></b>
      </td>
      <td style="text-align:left">
        <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed recipient</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul><p><a href="../events/transfer.md">more</a></p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}


# transferTo

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Allows a ticket holder to transfer its tokens to another account, without unstaking to ERC-20s.

  @dev
  Only a ticket holder or an operator can transfer its tokens.

  @param _recipient The recipient of the tokens.
  @param _holder The holder to transfer tokens from.
  @param _projectId The ID of the project whos tokens are being transfered.
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

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}


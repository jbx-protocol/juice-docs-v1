# claimFor

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Claims internal tokens by minting and distributing ERC20 tokens.

  @dev
  Anyone can claim tokens on behalf of a token owner.

  @param _holder The owner of the tokens to claim.
  @param _projectId The ID of the project whos tokens are being claimed.
  @param _amount The amount of tokens to claim.
*/
function claimFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override {
  // Get a reference to the project's ERC20 tokens.
  IJBToken _token = tokenOf[_projectId];

  // Tokens must have been issued.
  require(_token != IJBToken(address(0)), '0x24: NOT_FOUND');

  // Get a reference to the amount of unclaimed tokens.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // There must be enough unlocked unclaimed tokens to claim.
  require(_unclaimedBalance >= _amount, '0x25: INSUFFICIENT_FUNDS');

  // Subtract the claim amount from the holder's balance.
  unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;

  // Subtract the claim amount from the project's total supply.
  unclaimedTotalSupplyOf[_projectId] = unclaimedTotalSupplyOf[_projectId] - _amount;

  // Mint the equivalent amount of ERC20s.
  _token.mint(_holder, _amount);

  emit Claim(_holder, _projectId, _amount, msg.sender);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}


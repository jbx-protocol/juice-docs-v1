# mintFor

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Mint new tokens.

  @dev
  Only a project's current terminal can mint its tokens.

  @param _holder The address receiving the new tokens.
  @param _projectId The project to which the tokens belong.
  @param _amount The amount to mint.
  @param _preferClaimedTokens Whether ERC20's should be converted automatically if they have been issued.
*/
function mintFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) {
  // An amount must be specified.
  require(_amount > 0, '0x22: NO_OP');

  // Get a reference to the project's ERC20 tokens.
  IJBToken _token = tokenOf[_projectId];

  // If there exists ERC-20 tokens and the caller prefers these claimed tokens or the project requires it.
  bool _shouldClaimTokens = (requireClaimFor[_projectId] || _preferClaimedTokens) &&
    _token != IJBToken(address(0));

  if (_shouldClaimTokens) {
    // Mint the equivalent amount of ERC20s.
    _token.mint(_holder, _amount);
  } else {
    // Add to the unclaimed balance and total supply.
    unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] + _amount;
    unclaimedTotalSupplyOf[_projectId] = unclaimedTotalSupplyOf[_projectId] + _amount;
  }

  emit Mint(_holder, _projectId, _amount, _shouldClaimTokens, _preferClaimedTokens, msg.sender);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}




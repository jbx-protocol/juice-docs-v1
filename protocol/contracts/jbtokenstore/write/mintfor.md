# mintFor

Contract:[`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Mint new tokens.**

_Only a project's current controller can mint its tokens._

Definition:

```solidity
function mintFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) { ... }
```

* Arguments:
  * `_holder` is the address receiving the new tokens.
  * `_projectId` is the ID of the project to which the tokens belong.
  * `_amount` is the amount of tokens to mint.
  * `_preferClaimedTokens` is a flag indicating whether there's a preference for ERC20's to be claimed automatically if they have been issued.
* Through the [`onlyController`](../../jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.



1. Make sure a positive amount was specified.

   ```solidity
   // An amount must be specified.
   require(_amount > 0, '0x22: NO_OP');
   ```
2. Get a reference to the project's token.

   ```solidity
   // Get a reference to the project's ERC20 tokens.
   IJBToken _token = tokenOf[_projectId];
   ```

   _Internal references:_

   * [`tokenOf`](../properties/tokenof.md)
3. Check if tokens should be minted using the internal accounting mechanism, or if they should be claimed into the holder's wallet. Tokens should be claimed if the project has issued tokens, and either the project forces tokens to be claimed or if `_preferClaimedTokens` flag is true. The internal accounting mechanism uses less gas, and can later be claimed into the holders wallet by anyone who submits a [`claimFor`](../claimfor/) transaction.

   ```solidity
   // If there exists ERC-20 tokens and the caller prefers these claimed tokens or the project requires it.
   bool _shouldClaimTokens = (requireClaimFor[_projectId] || _preferClaimedTokens) &&
     _token != IJBToken(address(0));
   ```

   _Internal references:_

   * [`requireClaimFor`](../properties/requireclaimfor.md)
4. If claimed tokens should be minted, mint the project's token into the holders wallet. Otherwise increment the holder's balance or the unclaimed tokens for the project, and the total supply of unclaimed tokens for the project.

   ```solidity
   if (_shouldClaimTokens) {
     // Mint the equivalent amount of ERC20s.
     _token.mint(_holder, _amount);
   } else {
     // Add to the unclaimed balance and total supply.
     unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] + _amount;
     unclaimedTotalSupplyOf[_projectId] = unclaimedTotalSupplyOf[_projectId] + _amount;
   }
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
   * [`unclaimedTotalSupplyOf`](../properties/unclaimedtotalsupplyof.md)
*  Emit a `Mint` event with the all relevant parameters.

   ```solidity
   emit Mint(_holder, _projectId, _amount, _shouldClaimTokens, _preferClaimedTokens, msg.sender);
   ```

   _Event references:_

   * [`Mint`](../events/mint.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Mint new tokens.

  @dev
  Only a project's current controller can mint its tokens.

  @param _holder The address receiving the new tokens.
  @param _projectId The ID of the project to which the tokens belong.
  @param _amount The amount of tokens to mint.
  @param _preferClaimedTokens A flag indicating whether there's a preference for ERC20's to be claimed automatically if they have been issued.
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

{% tab title="Errors" %}
| String            | Description                             |
| ----------------- | --------------------------------------- |
| **`0x22: NO_OP`** | Thrown if an amount of 0 was passed in. |
{% endtab %}

{% tab title="Events" %}
| Name                            | Data                                                                                                                                                                                                                                                                   |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Mint`**](../events/mint.md) | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>bool tokensWereClaimed</code></li><li><code>bool preferClaimedTokens</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

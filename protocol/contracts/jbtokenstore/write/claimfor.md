# claimFor

Contract:[`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Claims internal tokens by minting and distributing ERC20 tokens.**

_Anyone can claim tokens on behalf of a token owner._

# Definition

```solidity
function claimFor(
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override { ... }
```

* Arguments:
  * `_holder` is the owner of the tokens to claim.
  * `_projectId` is the ID of the project whose tokens are being claimed.
  * `_amount` is the amount of tokens to claim.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.

# Body 

1. Get a reference to the project's token.

   ```solidity
   // Get a reference to the project's ERC20 tokens.
   IJBToken _token = tokenOf[_projectId];
   ```

   _Internal references:_

   * [`tokenOf`](../properties/tokenof.md)
2. Make sure the project has a token. If it doesn't, there's nowhere to claim tokens onto.

   ```solidity
   // Tokens must have been issued.
   require(_token != IJBToken(address(0)), '0x24: NOT_FOUND');
   ```
3. Get a reference to the amount of unclaimed tokens the holder has for the project.

   ```solidity
   // Get a reference to the amount of unclaimed tokens.
   uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
4. Make sure the holder has enough tokens to claim.

   ```solidity
   // There must be enough unlocked unclaimed tokens to claim.
   require(_unclaimedBalance >= _amount, '0x25: INSUFFICIENT_FUNDS');
   ```
5. Subtract from the `unclaimedBalanceOf` the holder for the project.

   ```solidity
   // Subtract the claim amount from the holder's balance.
   unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;
   ```

   _Internal references:_

   * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
6. Subtract from the `unclaimedTotalSupplyOf` the project.

   ```solidity
   // Subtract the claim amount from the project's total supply.
   unclaimedTotalSupplyOf[_projectId] = unclaimedTotalSupplyOf[_projectId] - _amount;
   ```

    _Internal references:_

   * [`unclaimedTotalSupplyOf`](../properties/unclaimedtotalsupplyof.md)
7. Mint the tokens to the holders wallet.

   ```solidity
   // Mint the equivalent amount of ERC20s.
   _token.mint(_holder, _amount);
   ```
8. Emit a `Claim` event with the all relevant parameters.

   ```solidity
   emit Claim(_holder, _projectId, _amount, msg.sender);
   ```

   _Event references:_

   * [`Claim`](../events/claim.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Claims internal tokens by minting and distributing ERC20 tokens.

  @dev
  Anyone can claim tokens on behalf of a token owner.

  @param _holder The owner of the tokens to claim.
  @param _projectId The ID of the project whose tokens are being claimed.
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

{% tab title="Errors" %}
| String                         | Description                                               |
| ------------------------------ | --------------------------------------------------------- |
| **`0x24: NOT_FOUND`**          | Thrown if the project hasn't yet issued its token.        |
| **`0x25: INSUFFICIENT_FUNDS`** | Thrown if the holder doens't have enough tokens to claim. |
{% endtab %}

{% tab title="Events" %}
| Name                              | Data                                                                                                                                                                         |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Claim`**](../events/claim.md) | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}


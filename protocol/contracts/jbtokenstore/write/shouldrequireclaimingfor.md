# shouldRequireClaimingFor

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}
**Allows an unclaimed token holder to transfer them to another account, without claimed them to ERC-20s.**

_Only a token holder or an operator can transfer its unclaimed tokens_.

Definition:

```solidity
function shouldRequireClaimingFor(uint256 _projectId, bool _flag)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REQUIRE_CLAIM) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being affected.
  * `_flag` is a flag indicating whether or not claiming should be required.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.REQUIRE_CLAIM`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.



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
   require(_token != IJBToken(address(0)), '0x2a: NOT_FOUND');
   ```


3. Store the flag for the project.

   ```solidity
   // Store the flag.
   requireClaimFor[_projectId] = _flag;
   ```

   _Internal references:_

   * [`requireClaimFor`](../properties/requireclaimfor.md)
  

4. Emit a `ShouldRequireClaim` event with the all relevant parameters.

   ```solidity
   emit ShouldRequireClaim(_projectId, _flag, msg.sender);
   ```

   _Event references:_

   * [`ShouldRequireClaim`](../events/shouldrequireclaim.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Allows a project to force all future mints to be claimed into the holder's wallet, or revoke the flag if it's already set.

  @dev
  Only a token holder or an operator can transfer its unclaimed tokens.

  @param _projectId The ID of the project being affected.
  @param _flag A flag indicating whether or not claiming should be required.
*/
function shouldRequireClaimingFor(uint256 _projectId, bool _flag)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REQUIRE_CLAIM)
{
  // Get a reference to the project's ERC20 tokens.
  IJBToken _token = tokenOf[_projectId];

  // Tokens must have been issued.
  require(_token != IJBToken(address(0)), '0x2a: NOT_FOUND');

  // Store the flag.
  requireClaimFor[_projectId] = _flag;

  emit ShouldRequireClaim(_projectId, _flag, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`0x2a: NOT_FOUND`** | Thrown if the project hasn't yet issued a token. |
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
      <td style="text-align:left"><b><code>ShouldRequireClaim</code></b>
      </td>
      <td style="text-align:left">
        <ul><li><code>uint256 indexed projectId</code></li><li><code>bool indexed flag</code></li><li><code>address caller</code></li></ul><p><a href="../events/shouldrequireclaim.md">more</a></p>
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




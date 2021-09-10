# pay

{% tabs %}
{% tab title="Step by step" %}
The function has the following definition:

```javascript
function pay(
    uint256 _projectId,
    address _beneficiary,
    uint256 _minReturnedTokens,
    bool _preferUnstakedTokens,
    string calldata _memo,
    bytes calldata _delegateMetadata
) external payable override returns (uint256);
```

* `_projectId` is the ID of the project being contribute to.
* `_beneficiary` is the address to mint tokens for and pass along to the funding cycle's data source and delegate.
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice
  Contribute ETH to a project.

  @dev
  The msg.value is the amount of the contribution in wei.

  @param _projectId The ID of the project being contribute to.
  @param _beneficiary The address to mint tokens for and pass along to the funding cycle's data source and delegate.
  @param _minReturnedTokens The minimum number of tokens expected in return.
  @param _preferUnstakedTokens A flag indicating whether the request prefers to issue tokens unstaked rather than staked.
  @param _memo A memo that will be included in the published event, and passed along the the funding cycle's data source and delegate.
  @param _delegateMetadata Bytes to send along to the delegate, if one is provided.

  @return The number of the funding cycle that the payment was made during.
*/
function pay(
    uint256 _projectId,
    address _beneficiary,
    uint256 _minReturnedTokens,
    bool _preferUnstakedTokens,
    string calldata _memo,
    bytes calldata _delegateMetadata
) external payable override returns (uint256) {
    return
        _pay(
            msg.value,
            _projectId,
            _beneficiary,
            _minReturnedTokens,
            _preferUnstakedTokens,
            _memo,
            _delegateMetadata
        );
}
```
{% endtab %}
{% endtabs %}




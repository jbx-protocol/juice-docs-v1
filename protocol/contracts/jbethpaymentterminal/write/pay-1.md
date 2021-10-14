# pay

{% tabs %}
{% tab title="Step by step" %}
The function has the following definition:

```solidity
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

{% tab title="Code" %}
```solidity

```
{% endtab %}
{% endtabs %}




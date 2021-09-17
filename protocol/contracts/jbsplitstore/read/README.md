# Read

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Get all splits for the specified project ID, within the specified domain, for the specified group.

  @param _projectId The ID of the project to get splits for.
  @param _domain An identifier within which the returned splits should be considered active.
  @param _group The identifying group of the splits.

  @return An array of all splits for the project.
 */
function splitsOf(
    uint256 _projectId,
    uint256 _domain,
    uint256 _group
) external view override returns (Split[] memory) {
    return _splitsOf[_projectId][_domain][_group];
}
```
{% endtab %}
{% endtabs %}


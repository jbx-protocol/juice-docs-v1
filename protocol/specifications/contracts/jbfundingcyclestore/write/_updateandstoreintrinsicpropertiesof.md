# \_updateAndStoreIntrinsicPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Updates and stores intrinsic properties for a funding cycle.**

# Definition

```solidity
function _updateAndStoreIntrinsicPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  JBFundingCycle memory _baseFundingCycle,
  uint256 _mustStartOnOrAfter,
  uint256 _weight
) private { ... }
```

* Arguments:
  * `_configuration` is the configuration of the funding cycle being updated.
  * `_projectId` is the ID of the project whose funding cycle is being updated.
  * `_baseFundingCycle` is the cycle that the one being updated is based on.
  * `_mustStartOnOrAfter` is the time before which the new updated funding cycle can't start.
  * `_weight` is the weight to store along with a newly updated funding cycle.
* The function is private to this contract.
* The function doesn't return anything. 

# Body

1.  Find the time that the updated funding cycle should start at.

    ```solidity
    // Derive the correct next start time from the base.
    uint256 _start = _deriveStartFrom(_baseFundingCycle, _mustStartOnOrAfter);
    ```

    _Internal references:_

    * [`_deriveStartFrom`](../read/_derivestartfrom.md)
2.  Find the weight that the updated funding cycle should use. If a weight was provided to the function, use it. Otherwise derive one from the previous weight.\
    \
    If the provided weight is the number 1, treat is like the number 0. A weight of 0 means that no specific weight was passed in.

    ```solidity
    // A weight of 1 is treated as a weight of 0.
    _weight = _weight > 0
      ? (_weight == 1 ? 0 : _weight)
      : _deriveWeightFrom(_baseFundingCycle, _start);
    ```

    _Internal references:_

    * [`_deriveWeightFrom`](../read/_deriveweightfrom.md)
3.  Find the number that the updated funding cycle should use.

    ```solidity
    // Derive the correct number.
    uint256 _number = _deriveNumberFrom(_baseFundingCycle, _start);
    ```

    _Internal references:_

    * [`_deriveNumberFrom`](../read/_derivenumberfrom.md)
4.  Store the properties for the updated funding cycle.

    ```solidity
    // Update the intrinsic properties.
    fundingCycleId = _packAndStoreIntrinsicPropertiesOf(
      _configuration,
      _projectId,
      _number,
      _weight,
      _baseFundingCycle.configuration,
      _start
    );
    ```

    _Internal references:_

    * [`_packAndStoreIntrinsicPropertiesOf`](\_packandstoreintrinsicpropertiesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Updates and stores intrinsic properties for a funding cycle.

  @param _configuration The configuration of the funding cycle being updated.
  @param _projectId The ID of the project whose funding cycle is being updated.
  @param _baseFundingCycle The cycle that the one being updated is based on.
  @param _mustStartOnOrAfter The time before which the new updated funding cycle can't start.
  @param _weight The weight to store along with a newly updated funding cycle.
*/
function _updateAndStoreIntrinsicPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  JBFundingCycle memory _baseFundingCycle,
  uint256 _mustStartOnOrAfter,
  uint256 _weight
) private {
  // Derive the correct next start time from the base.
  uint256 _start = _deriveStartFrom(_baseFundingCycle, _mustStartOnOrAfter);

  // A weight of 1 is treated as a weight of 0.
  _weight = _weight > 0
    ? (_weight == 1 ? 0 : _weight)
    : _deriveWeightFrom(_baseFundingCycle, _start);

  // Derive the correct number.
  uint256 _number = _deriveNumberFrom(_baseFundingCycle, _start);

  // Update the intrinsic properties.
  _packAndStoreIntrinsicPropertiesOf(
    _configuration,
    _projectId,
    _number,
    _weight,
    _baseFundingCycle.configuration,
    _start
  );
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

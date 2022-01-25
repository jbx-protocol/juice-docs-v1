# isAllowedToSetController

Contract: [`JBDirectory`](../)‌

**Addresses that can set a project's controller. These addresses/contracts have been vetted and verified by Juicebox owners.** 

# Definition

```solidity
/**
  @notice
  Addresses that can set a project's controller. These addresses/contracts have been vetted and verified by Juicebox owners.
*/
mapping(address => bool) public override isAllowedToSetController;
```

* `_controller` is the address of the controller.
* The resulting view function is private to this contract.
 
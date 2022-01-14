# _setControllerAllowlist

Contract: [`JBDirectory`](../)‌

**A flag indicating the addresses that can set a project's controller.** 

_These addresses/contracts have been vetted and verified by JuiceboxDAO**_

# Definition

```solidity
/** 
  @notice 
  A flag indicating the addresses that can set a project's controller. 
  
  @dev
  These addresses/contracts have been vetted and verified by JuiceboxDAO.

  _controller The address of the controller.
*/
mapping(address => bool) private _setControllerAllowlist;
```

* `_controller` is the address of the controller.
* The resulting view function is private to this contract.
 
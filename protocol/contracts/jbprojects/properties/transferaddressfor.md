# transferAddressFor

Contract: [`JBProjects`](../)

Interface: ****`IJBProjects`

**The address that can reallocate a handle that have been transferred to it.**

Definition:

```javascript
/** 
  @notice 
  The address that can reallocate a handle that have been transferred to it.
*/
mapping(bytes32 => address) public override transferAddressFor;
```

* `bytes32` is the handle to look for the transfer address for.


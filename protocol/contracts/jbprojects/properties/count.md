# count

Contract: [`JBProjects`](../)

Interface: ****`IJBProjects`

**The number of projects that have been created using this contract.** 

_The count is incremented with each new project created. The resulting ERC-721 token ID for each project is the newly incremented `count` value._  
****  
Definition:

```javascript
/** 
  @notice 
  The number of projects that have been created using this contract.
  
  @dev
  The count is incremented with each new project created. 
  The resulting ERC-721 token ID for each project is the newly incremented count value.
*/ 
uint256 public override count = 0;
```


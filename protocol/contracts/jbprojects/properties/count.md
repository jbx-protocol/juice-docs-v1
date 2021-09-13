# count

_**Contract:**_[`JBProjects`](../)

**Interface:** `IJBProjects`

**The number of projects that have been created using this contract.** 

_The count is incremented with each new project created. The resulting ERC-721 token ID for each project is the newly incremented `count`value._  
****  
Definition:

```javascript
/** 
  @notice 
  A running count of project IDs.
*/ 
uint256 public override count = 0;
```


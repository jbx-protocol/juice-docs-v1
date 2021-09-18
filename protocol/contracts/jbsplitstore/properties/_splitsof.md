# \_splitsOf

Contract:[`JBSplitStore`](../)​‌

Interface: `IJBSplitStore`

**All splits for each project ID's configurations.**  
  
Definition:

```javascript
/** 
  @notice
  All splits for each project ID's configurations.
*/
mapping(uint256 => mapping(uint256 => mapping(uint256 => Split[]))) private _splitsOf;
```

* `uint256` is the ID of the project to get splits for.
* `uint256` is an identifier within which the returned splits should be considered active.
* `uint256` is the identifying group of the splits.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBProjects` interface.


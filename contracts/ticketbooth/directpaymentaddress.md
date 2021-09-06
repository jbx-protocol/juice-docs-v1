# DirectPaymentAddress

### Constructor

```javascript
/** 
  @param _terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.
  @param _projectId The ID of the project to pay when this contract receives funds.
  @param _memo The memo to use when this contract forwards a payment to a terminal.
*/
constructor(
    ITerminalDirectory _terminalDirectory,
    uint256 _projectId,
    string memory _memo
)
```






# Integration Guide

## Creating a Juicebox project

Add the Juice contract dependency to your project:

```
$ yarn add @jbox/sol
```

Inherit from `JuiceboxProject` in your contract. You will need to provide a `Project ID` and `Terminal Directory` address in the `JuiceboxProject` constructor. More on what this means below.

```text
// SPDX-License-Identifier: MIT
pragma solidity 0.8.6;

import "@jbox/sol/contracts/abstract/JuiceboxProject.sol";

contract HelloWorldContract is JuiceboxProject {
  ...
    
  constructor(
      uint256 _projectID,
      ITerminalDirectory _terminalDirectory
    ) JuiceboxProject(_projectID, _terminalDirectory) {}

  ...
}
```




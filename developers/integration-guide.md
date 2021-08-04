# Integration Guide

## Hooking up your contract to a Juicebox project

Add the Juicebox contract dependency to your project:

```text
$ yarn add @jbox/sol
```

Inherit from `JuiceboxProject` in your contract. You will need to provide a `Project ID` and `Terminal Directory` address in the `JuiceboxProject` constructor.

In the meantime, feel free to refer to some examples:

* [TileDAO](https://github.com/TileDAO/tiles/blob/main/contracts/Tiles.sol)
* [WikiToken](https://github.com/odd-amphora/wiki.token/blob/main/packages/hardhat/contracts/Token.sol)

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

TODO


---
description: >-
  Risks to understand before interacting with the protocol. 
---
# Risks 

The following are risks that everyone should be aware of before interacting with the protocol. The protocol's design exposes these risks in consequence to its normal operating procedures. Most risks boil down to the trusting of governance processes. 

WIP

* **`The owner of the project NFT can reconfigure the project's funding cycle`**\
  * **`Reconfigurations can include:`**\
  * **`custom treasury extension implementations`**\
  * **`distributions from the project's treasury`**\
  * **`reduction of overflow from which redeemed tokens can be reclaimed`**\
  * **`the ability for projects to migrate funds to custom terminals`**\
  * **`the ability for projects to add new terminals with which it can manage inflows and outflows of funds`**\
  * **`the ability for projects to change the controller which manages its operating constraints`**\
  * **`the ability to mint arbitrary amounts of tokens for arbitrary beneficiaries`**\
  * **`the ability for projects to change the token that is currenctly being minted/burned by the protocol`**\
  * **`a change in reserved tokens that are distributable`**\
* **`The owner of the project NFT can change the splits that aren't locked at any time`**\
* **`If a project's treasury references in multiple currencies, price oracles are used to resolve conversions`**\
---
description: Testing contracts on a local blockchain node
---

# Local Testing

This tutorial should familiarize you with starting a local blockchain using Hardhat, deploying a sample smart contract to the local blockchain and interacting with that blockchain with Metamask and Remix.

#### Why local blockchain?

* It is very useful to run a local blockchain because testing becomes very fast and efficient.&#x20;
* It is only your machine which is running the blockchain and thus consensus is fast and you dont have to wait for other nodes to sync or validate.&#x20;
* You can also use many specialized modules specially built for local testing like [Hardhat console.log](https://hardhat.org/tutorial/debugging-with-hardhat-network.html) which helps you to add printing inside your contract.

### Build

To build the smart contract we would be using [Hardhat](https://hardhat.org/).

* Hardhat is an Ethereum development environment and framework designed for full stack development in Solidity
*   &#x20;You can write your smart contract, deploy them, run tests, and debug your code.



**Setup a Hardhat project**

```shell
mkdir testing_tutorial
cd testing_tutorial
npm init --yes
npm install --save-dev hardhat

# bootstrap the hardhat project
npx hardhat

# if not on mac, you need also the below
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

* Select `Create a JavaScript project`
* Press enter for the already specified `Hardhat Project root`
* Press enter for the question on if you want to add a `.gitignore`
* Press enter for `Do you want to install this sample project's dependencies with npm @nomicfoundation/hardhat-toolbox?`

Create**`Greeter.sol`**  inside **./contracts**

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract Greeter {
    string private greeting;

    constructor(string memory _greeting) {
        console.log("Deploying a Greeter with greeting:", _greeting);
        greeting = _greeting;
    }

    function greet() public view returns (string memory) {
        return greeting;
    }

    function setGreeting(string memory _greeting) public {
        console.log("Changing greeting from '%s' to '%s'", greeting, _greeting);
        greeting = _greeting;
    }
    
    receive() external payable{}
    fallback() external payable{}
}
```

### Metamask Connection



### Remix




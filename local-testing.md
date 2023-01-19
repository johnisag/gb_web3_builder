---
description: Testing contracts on a local blockchain node
---

# Local Testing

This tutorial should familiarize you with starting a local blockchain using Hardhat, deploying a sample smart contract to the local blockchain and interacting with that blockchain with Metamask and Remix.

#### Why local blockchain?

* It is very useful to run a local blockchain because testing becomes very fast and efficient.&#x20;
* It is only your machine which is running the blockchain and thus consensus is fast and you dont have to wait for other nodes to sync or validate.&#x20;
* You can also use many specialized modules specially built for local testing like [Hardhat console.log](https://hardhat.org/tutorial/debugging-with-hardhat-network.html) which helps you to add printing inside your contract.

### Smart Contract

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

* **`view` ** function, it costs no gas, and requires no signing to execute
* **`setGreeting` ** method updates the smart contract state, it costs gas, and requires signing
* **memory variables** live only within contract lifecycle
* **`setGreeting` ** uses the **Hardhat's console.log contract**, so **we can actually debug** and see to what values was `greeting` changed to!

**Running your local blockchain in your terminal pointing to your directory**&#x20;

```shell
npx hardhat node
```

This command starts a local blockchain node for you. You should be able to see some accounts which have already been funded by hardhat with 10000 ETH

**Create/replace** ./**`scripts`**/**`deploy.js`**.

```javascript
async function main() {
  const greeterContract = await ethers.getContractFactory("Greeter");

  // here we deploy the contract; we provide also constructor args
  const deployedGreeterContract = await greeterContract.deploy(
    "Set by the constructor"
  );
  await deployedGreeterContract.deployed();

  // print the address of the deployed contract
  console.log("Greeter Contract Address:", deployedGreeterContract.address);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

**Compile and deploy**

<pre class="language-shell"><code class="lang-shell"># If we experience Error: Cannot find module '@nomicfoundation/hardhat-toolbox', then
<strong># npm install --save-dev @nomicfoundation/hardhat-toolbox
</strong>
# from within hardhat folder
npx hardhat compile
npx hardhat run scripts/deploy.js
</code></pre>

### Metamask Connection



### Remix




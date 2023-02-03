---
description: Testing contracts on a local blockchain node
---

# Local Node Testing

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
# after start we should see something like:
# Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/
```

* Keep this terminal running
* Keep port used by HTTP and WebSocket JSON-RPC server
* We will use this port (8545 here) with metamask

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

Click on your profile and then click on settings

![](<.gitbook/assets/image (11).png>)

Then click on Networks, followed by **`Localhost 8545`**

``![](<.gitbook/assets/image (1) (2).png>)``



![](<.gitbook/assets/image (6).png>)



Change the **Chain ID to `31337`**(this is the chainId for the local blockchain you are running) and then click **`Save`**

**``**![](<.gitbook/assets/image (10).png>)**``**

* Now your MetaMask has a connection to your local blockchain&#x20;
* We will now add the accounts that Hardhat gave to us.&#x20;
* In the node terminal, you should see several accounts displayed. Let's grab one of those:

```
Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

* Go to **Metamask --> click on your profile --> Import Account.**&#x20;
* Select private key in the dropdown and paste the private key from the account you wish.&#x20;
* You should now see an account with 10000 ETH (wow!)



### Remix Connection

Go to [remix.ethereum.org](https://remix.ethereum.org/#optimize=false\&runs=200\&evmVersion=null\&version=soljson-v0.8.7+commit.e28d00a7.js) and create a new file inside the contracts folder named **`Greeter.sol`**

* Copy the previous contract that we created

**Compile `Greeter.sol`**

![](<.gitbook/assets/image (3).png>)

Now to deploy, go to deployment tab and in your environment select **`Injected Provider - Metamask`**, make sure that the account connected is the one that you imported above and the network is ** `Localhost 8545`** on your MetaMask

![](<.gitbook/assets/image (13).png>)

![](<.gitbook/assets/image (12).png>)

Set a greeting and click on deploy. Wait for it to finish. Your contract is now deployed&#x20;

Set a greeting and click on `setGreeting`

``![](<.gitbook/assets/image (5).png>)``

Check your terminal which was running your hardhat node, it should have the console.log

<figure><img src=".gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>


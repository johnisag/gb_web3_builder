---
description: Chainlink VRFs
---

# Lottery with LINK VRF

<figure><img src=".gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

### Introduction

When dealing with computers, **randomness** is an **important but difficult issue to handle** due to a computer's deterministic nature.&#x20;

**This is true even more so when speaking of blockchain** because not only is the computer deterministic, but it is also transparent.&#x20;

As a result, trusted random numbers cannot be generated natively in Solidity because randomness will be calculated on-chain which is public info to all the miners and the users.

So we can use some web2 technologies to generate the randomness and then use them on-chain.

### What is an oracle?

* An **oracle sends data from the outside world to a blockchain's smart contract** and vice-versa.
* Smart contracts can then use this data to make a decision and change its state.
* They **act as bridges between blockchains and the external world**.
* However it is important to note that the **blockchain oracle is not itself the data source** but its job is to **query**, **verify** and **authenticate** the **outside data** and then further pass it to the smart contract.

### Intro

* **Chainlink VRF's are oracles used to generate random values**.
* These **values are verified using cryptographic proofs**.
* These proofs **prove that the results weren't tampered** or manipulated by oracle operators, users, miners etc.
* **Proofs** are **published on-chain so that they can be verified**.
* After there verification is successful they are used by smart contracts which requested randomness.

_**The official Chainlink Docs describe VRFs as:**_

> Chainlink VRF (Verifiable Random Function) is a provably-fair and verifiable source of randomness designed for smart contracts. Smart contract developers can use Chainlink VRF as a tamper-proof random number generator (RNG) to build reliable smart contracts for any applications which rely on unpredictable outcomes.

### How does it work?

* Chainlink has two contracts that we are mostly concerned about [VRFConsumerBase.sol](https://github.com/smartcontractkit/chainlink/blob/master/contracts/src/v0.8/VRFConsumerBase.sol) and VRFCoordinator
* VRFConsumerBase is the contract that will be calling the VRF Coordinator which is finally reponsible for publishing the randomness
* We will be inheriting VRFConsumerBase and will be using two functions from it:
  * **requestRandomness**, which makes the initial request for randomness.
  * **fulfillRandomness**, which is the function that receives and does something with verified randomness.

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

* If you look at the diagram you can understand the flow, `RandomGameWinner` contract will inherit the `VRFConsumerBase` contract and will call the `requestRandomness` function within the `VRFConsumerBase`.
* On calling that function the request to randomness starts and the `VRFConsumerBase` further calls the `VRFCoordinator` contract which is reponsible for getting the randomness back from the external world.
* After the `VRFCoordinator` has the randomness it calls the `fullFillRandomness` function within the `VRFConsumerBase` which further then selects the winner.
* **Note the important part is that eventhough you called the `requestRandomness` function you get the randomness back in the `fullFillRandomness` function**

### Requirements

* We will build a lottery game today
* Each game will have a max number of players and an entry fee
* After max number of players have entered the game, one winner is chosen at random
* The winner will get `maxplayers*entryfee` amount of ether for winning the game

### Contract

**Setup a Hardhat project**

```shell
mkdir random_winner_game
cd random_winner_game
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

# bootstrap the hardhat project
npx hardhat
```

Make sure you select **`Create a Javascript Project`** and then follow the steps in the terminal to complete your Hardhat setup.

Install **`@openzeppelin/contracts`** as we would be importing [**Openzeppelin's** ](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)as we will import **Ownable.sol**

```shell
npm install @openzeppelin/contracts
```

Install chainlink contracts, to use Chainlink VRF

```shell
npm install --save @chainlink/contracts
```

Create a new file inside the **`contracts`** directory and call it **`RandomWinnerGame.sol`**.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/VRFConsumerBase.sol";

contract RandomWinnerGame is VRFConsumerBase, Ownable {

    //Chainlink variables
    // The amount of LINK to send with the request
    uint256 public fee;
    // ID of public key against which randomness is generated
    bytes32 public keyHash;

    // Address of the players
    address[] public players;
    //Max number of players in one game
    uint8 maxPlayers;
    // Variable to indicate if the game has started or not
    bool public gameStarted;
    // the fees for entering the game
    uint256 entryFee;
    // current game id
    uint256 public gameId;

    // emitted when the game starts
    event GameStarted(uint256 gameId, uint8 maxPlayers, uint256 entryFee);
    // emitted when someone joins a game
    event PlayerJoined(uint256 gameId, address player);
    // emitted when the game ends
    event GameEnded(uint256 gameId, address winner,bytes32 requestId);

   /**
   * constructor inherits a VRFConsumerBase and initiates the values for keyHash, fee and gameStarted
   * @param vrfCoordinator address of VRFCoordinator contract
   * @param linkToken address of LINK token contract
   * @param vrfFee the amount of LINK to send with the request
   * @param vrfKeyHash ID of public key against which randomness is generated
   */
    constructor(address vrfCoordinator, address linkToken,
    bytes32 vrfKeyHash, uint256 vrfFee)
    VRFConsumerBase(vrfCoordinator, linkToken) {
        keyHash = vrfKeyHash;
        fee = vrfFee;
        gameStarted = false;
    }

    /**
    * startGame starts the game by setting appropriate values for all the variables
    */
    function startGame(uint8 _maxPlayers, uint256 _entryFee) public onlyOwner {
        // Check if there is a game already running
        require(!gameStarted, "Game is currently running");
        // empty the players array
        delete players;
        // set the max players for this game
        maxPlayers = _maxPlayers;
        // set the game started to true
        gameStarted = true;
        // setup the entryFee for the game
        entryFee = _entryFee;
        gameId += 1;
        emit GameStarted(gameId, maxPlayers, entryFee);
    }

    /**
    joinGame is called when a player wants to enter the game
     */
    function joinGame() public payable {
        // Check if a game is already running
        require(gameStarted, "Game has not been started yet");
        // Check if the value sent by the user matches the entryFee
        require(msg.value == entryFee, "Value sent is not equal to entryFee");
        // Check if there is still some space left in the game to add another player
        require(players.length < maxPlayers, "Game is full");
        // add the sender to the players list
        players.push(msg.sender);
        emit PlayerJoined(gameId, msg.sender);
        // If the list is full start the winner selection process
        if(players.length == maxPlayers) {
            getRandomWinner();
        }
    }

    /**
    * fulfillRandomness is called by VRFCoordinator when it receives a valid VRF proof.
    * This function is overrided to act upon the random number generated by Chainlink VRF.
    * @param requestId  this ID is unique for the request we sent to the VRF Coordinator
    * @param randomness this is a random unit256 generated and returned to us by the VRF Coordinator
   */
    function fulfillRandomness(bytes32 requestId, uint256 randomness) internal virtual override  {
        // We want out winnerIndex to be in the length from 0 to players.length-1
        // For this we mod it with the player.length value
        uint256 winnerIndex = randomness % players.length;
        // get the address of the winner from the players array
        address winner = players[winnerIndex];
        // send the ether in the contract to the winner
        (bool sent,) = winner.call{value: address(this).balance}("");
        require(sent, "Failed to send Ether");
        // Emit that the game has ended
        emit GameEnded(gameId, winner,requestId);
        // set the gameStarted variable to false
        gameStarted = false;
    }

    /**
    * getRandomWinner is called to start the process of selecting a random winner
    */
    function getRandomWinner() private returns (bytes32 requestId) {
        // LINK is an internal interface for Link token found within the VRFConsumerBase
        // Here we use the balanceOF method from that interface to make sure that our
        // contract has enough link so that we can request the VRFCoordinator for randomness
        require(LINK.balanceOf(address(this)) >= fee, "Not enough LINK");
        // Make a request to the VRF coordinator.
        // requestRandomness is a function within the VRFConsumerBase
        // it starts the process of randomness generation
        return requestRandomness(keyHash, fee);
    }

     // Function to receive Ether. msg.data must be empty
    receive() external payable {}

    // Fallback function is called when msg.data is not empty
    fallback() external payable {}
}
```

The constructor takes in the following params:

* `vrfCoordinator` which is the address of the VRFCoordinator contract
* `linkToken` is the address of the link token which is the token in which the chainlink takes its payment
* `vrfFee` is the amount of link token that will be needed to send a randomness request
* `vrfKeyHash` which is the ID of the public key against which randomness is generated. This value is responsible for generating an unique Id for our randomneses request called as `requestId`

**(All these values are provided to us by Chainlink)**

Install **`dotenv` ** package to be able to import the env file and use it in our config.&#x20;

Inside **`hardhat-tutorial`** directory:

```shell
npm install dotenv
```

Create a `.env` file in the `hardha_tutorial` folder and add the following lines

```javascript
QUICKNODE_HTTP_URL="add-quicknode-http-provider-url-here"
PRIVATE_KEY="add-the-private-key-here"
POLYGONSCAN_KEY="polygonscan-api-key-token-here"
```

Go to [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) and sign up for an account.&#x20;

* Quicknode is a node provider that lets you connect to various different blockchains.
* We will be using it to deploy our contract through Hardhat

**Steps:**

1. **Create an account** in  [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3)&#x20;
2. **`Create an endpoint`** on  [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3)&#x20;
   * Select <mark style="color:orange;">**`Polygon`**</mark>, and then&#x20;
   * Select the <mark style="color:purple;">**`Mumbai`**</mark><mark style="color:purple;">** **</mark><mark style="color:purple;">****</mark> network (testnet)
   * `Click`` `**`Continue` ** in the bottom right and then click on **`Create Endpoint`**
3. Copy the link given to you in **`HTTP Provider`** and add it to the **`.env`** file below for **`QUICKNODE_HTTP_URL`**

**To get your private key, you need to export it from Metamask**.&#x20;

* Open Metamask, click on the three dots,&#x20;
* click on `Account Details` and then `Export Private Key`.&#x20;
* MAKE SURE YOU ARE USING A TEST ACCOUNT THAT DOES NOT HAVE MAINNET FUNDS FOR THIS.&#x20;
* Add this Private Key below in your `.env` file for `PRIVATE_KEY` variable.

**Make sure you have some Mumbai `MATIC` tokens to work with**

* If you don't know how to get them, follow [this guide by ThirdWeb](https://portal.thirdweb.com/guides/get-matic-on-polygon-mumbai-testnet-faucet).

**Similar to Etherscan, the Polygon network has Polygonscan.**

* Both block explorers are developed by the same team and work basically exactly the same way.&#x20;
* To automate contract verification on Polygonscan through Hardhat, we will need an API Key for Polygonscan.&#x20;
* **Steps**
  * Go to [PolygonScan](https://polygonscan.com/) and sign up.&#x20;
  * On the Account Overview page, click on **`API Keys`**, **add a new API Key**, and **copy the `API Key Token`.**&#x20;
  * Put this in **`POLYGONSCAN_KEY` ** on **.env**.

Update **hardhat.config.js** file to use the <mark style="color:purple;">**mumbai**</mark> network and use **etherscan** object for contract verification on <mark style="color:blue;">**polygonscan**</mark>

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const QUICKNODE_HTTP_URL = process.env.QUICKNODE_HTTP_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;
const POLYGONSCAN_KEY = process.env.POLYGONSCAN_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    mumbai: {
      url: QUICKNODE_HTTP_URL,
      accounts: [PRIVATE_KEY],
    },
  },
  etherscan: {
    apiKey: {
      polygonMumbai: POLYGONSCAN_KEY,
    },
  },
};

```

Create a new folder named as **`constants` ** and inside that add a new file named **`index.js`**

* The values we got for this are from [here](https://blog.chain.link/how-to-get-a-random-number-on-polygon) and are already provided to us by Chainlink

```javascript
const { ethers, BigNumber } = require("hardhat");

const LINK_TOKEN = "0x326C977E6efc84E512bB9C30f76E30c160eD06FB";
const VRF_COORDINATOR = "0x8C7382F9D8f56b33781fE506E897a4F1e2d17255";
const KEY_HASH =
  "0x6e75b569a01ef56d18cab6a8e71e6600d6ce853834d4a5748b720d06f878b3a4";
const FEE = ethers.utils.parseEther("0.0001");
module.exports = { LINK_TOKEN, VRF_COORDINATOR, KEY_HASH, FEE };
```

Create/replace ./**`scripts`**/**`deploy.js`**.

* Deployment
* Contract verification&#x20;

```javascript
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });
const { FEE, VRF_COORDINATOR, LINK_TOKEN, KEY_HASH } = require("../constants");

async function main() {
  /*
 A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
 so randomWinnerGame here is a factory for instances of our RandomWinnerGame contract.
 */
  const randomWinnerGame = await ethers.getContractFactory("RandomWinnerGame");
  // deploy the contract
  const deployedRandomWinnerGameContract = await randomWinnerGame.deploy(
    VRF_COORDINATOR,
    LINK_TOKEN,
    KEY_HASH,
    FEE
  );

  await deployedRandomWinnerGameContract.deployed();

  // print the address of the deployed contract
  console.log(
    "Verify Contract Address:",
    deployedRandomWinnerGameContract.address
  );

  console.log("Sleeping.....");
  // Wait for etherscan to notice that the contract has been deployed
  await sleep(30000);

  // Verify the contract after deploying
  await hre.run("verify:verify", {
    address: deployedRandomWinnerGameContract.address,
    constructorArguments: [VRF_COORDINATOR, LINK_TOKEN, KEY_HASH, FEE],
  });
}

function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
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
npx hardhat run scripts/deploy.js --network mumbai
</code></pre>




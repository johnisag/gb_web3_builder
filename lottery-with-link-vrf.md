---
description: Chainlink VRFs
---

# Lottery with LINK VRF

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

Install **`@openzeppelin/contracts`** as we would be importing [**Openzeppelin's ERC721Enumerable Contract**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)&#x20;

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

****

****

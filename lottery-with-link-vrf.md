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

****

****

****

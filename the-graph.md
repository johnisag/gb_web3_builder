# The Graph

****[**The Graph**](https://thegraph.com/) **is a decentralized query protocol and indexing service for the blockchain.**&#x20;

* It allows developers to easily track events being emitted from smart contracts on various networks
* Write custom data transformation scripts, which are run in real time.&#x20;
* The data is also made available through a simple GraphQL API which developers can then use to display things on their frontends.

### Prerequisites

* We will be using **yarn** which **is a package manager** just **like npm**.
  * **Install yarn from** [**here**](https://classic.yarnpkg.com/lang/en/docs/install/#mac-stable) ****&#x20;
* Watch this 40 minute tutorial on [GraphQL](https://www.youtube.com/watch?v=ZQL7tL2S0oQ)
* **What axios is**
  * Watch this short [tutorial](https://www.youtube.com/watch?v=6LyagkoRWYA)
* You should have completed the [Chainlink VRF tutorial](https://github.com/LearnWeb3DAO/Chainlink-VRFs)

### How it Works

<figure><img src=".gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

1. **A dApp sends a transaction** and **some data gets stored** in the **smart contract**.&#x20;
2. This **smart contract then emits** one or more **events**.
3. **Graph's node keeps scanning Ethereum** for new blocks and the data for your subgraph that these blocks may contain.
4. **If the node finds an event you were looking for and defined in your subgraph, it runs the data transformation scripts (mappings) you defined**. The mapping is a WASM (Web assembly) module that creates or updates data `Entities` on the Graph Nodes in response to the event.
5. We can query the Graph's node for this data using the [GraphQL Endpoint](https://graphql.org/learn/)

### Preparation

We will be using the folder named **`RandomWinnerGame` ** that you created in the ** `Chainlink VRF`** tutorial

* **Create** an **abi.json file inside your `RandomWinnerGame` folder**&#x20;
  * You will need this file to intilialize your graph and&#x20;
* **Copy the following** [content](https://github.com/LearnWeb3DAO/Graph/blob/master/abi.json)**.**

_Note this is the abi of the **`RandomWinnerGame`**** ****** contract you created in the Chainlink VRF tutorial_

So your final folder structure should look like this:

```
RandomWinnerGame    
    - hardhat-tutorial    
    - abi.json
```

### **Create the subgraph** &#x20;

* Go to [**The Graph's Hosted Service**](https://thegraph.com/hosted-service/)****
* **Login using your GitHub account**&#x20;
* Visit **`My Dashboard`** tab
* Click on **`Add Subgraph`**, fill in the information and create the subgraph
* When your subgraph is created, it will show you some commands in `Install`, `Init` and `Deploy`

In your terminal execute this command pointing to **`RandomWinnerGame` ** folder:

```sh
yarn global add @graphprotocol/graph-cli
```

**If this doesn't work**, you can try using `npm`.&#x20;

```sh
npm install -g @graphprotocol/graph-cli
```

### **Init the Graph**&#x20;

* Execute below command
* Replace `GITHUB_USERNAME` with your Github username and&#x20;
* Replace `YOUR_RANDOM_WINNER_GAME_CONTRACT_ADDRESS` with the address of the RandomWinnerGame contract that you deployed in your Chainlink VRF tutorial.&#x20;
* Press enter for all the questions (**select ethereum and mumbai**)

```sh
graph init --contract-name RandomWinnerGame --product hosted-service GITHUB_USERNAME/Learnweb3 --from-contract YOUR_RANDOM_WINNER_GAME_CONTRACT_ADDRESS --abi ./abi.json --network mumbai graph
```

<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

### **Deploy the graph**

* Go to [The Graph's Hosted Service](https://thegraph.com/hosted-service/),&#x20;
* Click on **`My Dashboard`**
* `Select your subgraph`
* Copy the **`Access Token`**
* Paste it for the `Deploy Key`

```sh
graph auth --product hosted-service ACCCESS_TOKEN
```

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* Execute the below command for deployment:

```sh
cd graph
yarn deploy
# or `npm run deploy`
# or graph deploy --product hosted-service johnisag/ia_learnweb3
```

<figure><img src=".gitbook/assets/image (2) (5) (1).png" alt=""><figcaption></figcaption></figure>

### **Setting the Entities**&#x20;

* Open up you `subgraph.yaml` inside the `graph` folder
* Add a `startBlock` to the yaml file after the `abi: RamdomWinnerGame` line&#x20;
* To get the startBlock you will need to go to [Mumbai PolygonScan](https://mumbai.polygonscan.com/) and search up your contract address
* After that you will need to copy the block number of the block in which your contract was deployed

_The start block doesn't come with the default settings but because we know that we only need to track the events from the block the contract was deployed, we will not need to sync the entire blockchain but only the part after the contract was deployed for tracking the events_

<figure><img src=".gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>

```
source:  
    address: "RANDOM_GAME_CONTRACT_ADDRESS"  
    abi: RandomWinnerGame  
    startBlock: BLOCK_NUMBER
```

Your final file should look something like [this](https://github.com/LearnWeb3DAO/Graph/blob/master/graph/subgraph.yaml#L11)

Its time to C**reate** some **`Entities`**.&#x20;

* **`Entities` ** are **objects** which **define** the structure for **how your data will be stored** on **`The Graph's nodes`**.&#x20;
  * If you want to read more about them, click on this [link](https://thegraph.com/docs/en/developer/create-subgraph-hosted/#defining-entities)

We will need an **`Entity` which can cover all the variables we have in our events** so that we can keep track of all of them.&#x20;

Open up for **`schema.graphql`** file and replace with following:

```graphql
type Game @entity {
  id: ID!
  maxPlayers: Int!
  entryFee: BigInt!
  winner: Bytes
  requestId: Bytes
  players: [Bytes!]!
}
```

* **`ID`** is the unique identifier for a game and will be equivalent to the **`gameId` ** variable we have in our contract.
* **`maxPlayers` ** will keep track of how many max players are allowed in this game.
* **`entryFee` ** is the fees to enter into the game and it is a BigInt because we have **`entryFee` ** as a **`uint256` ** in our contract which is a **BigNumber**
* **`winner` ** is the address of the winner in the game and is defined as **Bytes** because address is a **hexadecimal string**
* **`requestId` ** is also a **hexadecimal string** and thus is defined as **`Bytes`**
* **`players` ** is the list of addresses for the players in the game and because each address is a hexadecimal string, we have signified players as a Bytes array
* Also note that **`!`** indicates **required variables**, we have **`maxPlayers`**, **`entryFee`**, **`players` ** and **`id` ** marked as required because when the **`Game` ** initially starts it will emit the **`GameStarted` ** event which will emit these three variables(**`maxPlayers`**, **`entryFee` ** and **`id`**) so a **`Game` ** entity can never be created without these three variables and for the players array, it will be initialized to an empty array by us.
* **`winner` ** and **`requestId` ** will come with **`GameEnded` ** event and **`players` ** will keep track of every **`player address`** which is emitted by the **`PlayerJoined`** event

_**If you want to learn more about the types you can visit this**_ [_**link**_](https://thegraph.com/docs/en/developer/create-subgraph-hosted/#built-in-scalar-types)_****_

_<mark style="color:orange;">Now we have let the graph know what kind of data we will be tracking and what will it contain</mark>_&#x20;

### Handling our Events

_Graph has an amazing functionality that given the `Entity` it can auto generate large chunk of code for you!!!_

In your terminal pointing to the graph directory execute this command

```sh
yarn codegen
# or npm run codegen
```

After the above, **`The Graph`** would have created most of the code for you **expect of the mappings.**&#x20;

If you look at the file inside ** `src`** named **`random-winner-game.ts`**,&#x20;

**The Graph CLI would have created** for you **some functions** **each pointing** to **one of the events that you created in your contract.**&#x20;

**These functions get called everytime the Graph finds an event relating to these functions**.&#x20;

We will add some code to these functions so that we can store the data when an event comes in.

Replace the **`random-winner-game.ts`** file content&#x20;

```typescript
import { BigInt } from "@graphprotocol/graph-ts";
import {
  PlayerJoined,
  GameEnded,
  GameStarted,
  OwnershipTransferred,
} from "../generated/RandomWinnerGame/RandomWinnerGame";
import { Game } from "../generated/schema";

export function handleGameEnded(event: GameEnded): void {
  // Entities can be loaded from the store using a string ID; this ID
  // needs to be unique across all entities of the same type
  let entity = Game.load(event.params.gameId.toString());
  // Entities only exist after they have been saved to the store;
  // `null` checks allow to create entities on demand
  if (!entity) {
    return;
  }
  // Entity fields can be set based on event parameters
  entity.winner = event.params.winner;
  entity.requestId = event.params.requestId;
  // Entities can be written to the store with `.save()`
  entity.save();
}

export function handlePlayerJoined(event: PlayerJoined): void {
  // Entities can be loaded from the store using a string ID; this ID
  // needs to be unique across all entities of the same type
  let entity = Game.load(event.params.gameId.toString());
  // Entities only exist after they have been saved to the store;
  // `null` checks allow to create entities on demand
  if (!entity) {
    return;
  }
  // Entity fields can be set based on event parameters
  let newPlayers = entity.players;
  newPlayers.push(event.params.player);
  entity.players = newPlayers;
  // Entities can be written to the store with `.save()`
  entity.save();
}

export function handleGameStarted(event: GameStarted): void {
  // Entities can be loaded from the store using a string ID; this ID
  // needs to be unique across all entities of the same type
  let entity = Game.load(event.params.gameId.toString());
  // Entities only exist after they have been saved to the store;
  // `null` checks allow to create entities on demand
  if (!entity) {
    entity = new Game(event.params.gameId.toString());
    entity.players = [];
  }
  // Entity fields can be set based on event parameters
  entity.maxPlayers = event.params.maxPlayers;
  entity.entryFee = event.params.entryFee;
  // Entities can be written to the store with `.save()`
  entity.save();
}

export function handleOwnershipTransferred(event: OwnershipTransferred): void {}
```

Lets understand what is happening in **`handleGameEnded` ** function

* it takes in the **`GameEnded` ** event and expects **`void` ** to be returned which means nothing gets returned from the function
* It loads a **`Game` ** object from **`Graph's` ** db which has an **ID** equal to the **`gameId` ** that is present in the event that Graph detected
* If an entity with the given **`id` ** doesn't exist return from the function and dont do anything
* If it exists, set the winner and the requestId from the event into our Game Object(Note **`GameEnded` ** event has the winner and requestId)
* Then save this updated Game Object back to the **`Graph's DB`**
* For each game there will be a unique **`Game` ** object which will have a unique **`gameId`**

Now lets see what's happening in the **`handlePlayerJoined`**

* It loads a **`Game` ** object from **`Graph's`** db which has an **ID** equal to the **`gameId` ** that is present in the event that Graph detected
* If an entity with the given **`id` ** doesn't exist, return from the function and dont do anything
* To actually update the player's array, we need to reassign the property on the entity that contains the array (i.e. players) similar to how we assign values to other properties on the entity (e.g. maxPlayers). Therefore, we need to create a temporary array which contains all of the existing entity.players elements, push to that, and reassign entity.players to be equal to the new array.
* Then save this updated Game Object back to the **`Graph's DB`**

Now lets see what's happening in the **`handleGameStarted`**

* It loads a **`Game` ** object from **`Graph's`** db which has an **ID** equal to the **`gameId` ** that is present in the event that Graph detected
* If an entity like that doesn't exist create a new one, also initialize the players array
* Then set the **maxPlayer** and the **entryFee** from the event into our Game Object
* Save this updated Game Object back to the **`Graph's DB`**

You can now go to your terminal pointing to the **`graph` ** folder and execute the following command:

```sh
yarn deploy
# or `npm run deploy`
```

After deploying, [The Graph's Hosted Service](https://thegraph.com/hosted-service/) and click on **`My Dashboard` ** you will be able to see your graph.

Click on your graph and make sure it says synced, if not wait for it to get synched before proceeding

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### dApp

**Bootstrap** the **Next js** project from the project root and run it (**http://localhost:3000**)

* Note use javascipt project

```shell
npx create-next-app@latest
cd my-app
npm run dev
```

Install **web3modal** and **ethers.js** libraries and **`axios` ** to send requests to the graph

```shell
npm install web3modal
npm i ethers@5
npm i axios
```

Update **Home.modules.css** into the **styles folder**

```css
.main {
  min-height: 90vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-family: "Courier New", Courier, monospace;
}

.footer {
  display: flex;
  padding: 2rem 0;
  border-top: 1px solid #eaeaea;
  justify-content: center;
  align-items: center;
}

.input {
  width: 200px;
  height: 100%;
  padding: 1%;
  margin: 2%;
  box-shadow: 0 0 15px 4px rgba(0, 0, 0, 0.06);
  border-radius: 10px;
}

.image {
  width: 70%;
  height: 50%;
  margin-left: 20%;
}

.title {
  font-size: 2rem;
  margin: 2rem 1rem;
}

.description {
  line-height: 1;
  margin: 2rem 1rem;
  font-size: 1.2rem;
}

.log {
  line-height: 1rem;
  margin: 1rem 1rem;
  font-size: 1rem;
}

.button {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 8px;
  width: 200px;
  cursor: pointer;
  margin: 2rem 1rem;
}
@media (max-width: 1000px) {
  .main {
    width: 100%;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
}

```

### Query the graph

* Create a new folder inside your **`my-app` ** folder and name it **`queries`**.&#x20;
* Inside the **`queries` ** folder create a new file named **`index.js` **&#x20;

```javascript
export function FETCH_CREATED_GAME() {
    return `query {
          games(orderBy:id, orderDirection:desc, first: 1) {
              id
              maxPlayers
              entryFee
              winner
              players
          }
      }`;
  }
```

* Created a **`GraphQL` ** query&#x20;
  * where we said we want a **`game` ** object&#x20;
  * where data is **ordered by Id**(which is the gameId)&#x20;
  * in **decending order** and&#x20;
  * we **want the first game** from this ordered data

**Example**

```json
  {
    "id": "1",
    "maxPlayers": 2,
    "entryFee": "10000000000000",
    "winner": "0xdb6eaffa95899b53b27086bd784f3bbfd58ff843"
  },
  {
    "id": "2",
    "maxPlayers": 2,
    "entryFee": "10000000000000",
    "winner": "0x01573df433484fcbe6325a0c6e051dc62ab107d1"
  },
  {
    "id": "3",
    "maxPlayers": 2,
    "entryFee": "100000000000000",
    "winner": "0x01573df433484fcbe6325a0c6e051dc62ab107d1"
  },
  {
    "id": "4",
    "maxPlayers": 2,
    "entryFee": "10",
    "winner": null
  }
```

**We want the latest game every single time. To get the latest game,**&#x20;

* you will first have to order them by Id and then&#x20;
* put this data in decending order so that gameId 4 can come at the top(It will be the current game) and then&#x20;
* we say `first:1` because we only want the gameId 4 object, we dont care about the old games.

**You can actually see this query working on the graph's hosted service**. Lets try to do that:

I have already deployed a graph, lets look at my graph and use the query to query it, Go to [this link](https://thegraph.com/hosted-service/subgraph/sneh1999/learnweb3)

Replace the example query with our query and click on the purple play button

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

We can go to your graph through **`My dashboard`** and do the same things&#x20;

**To use querring on our dapp we will need to write an axios post request so that we can get this data from the graph**

Create a new folder inside your **`my-app` ** folder and name it **`utils`**.&#x20;

Inside the **`utils`**folder create a new file named **`index.js` **&#x20;

```javascript
import axios from "axios";

export async function subgraphQuery(query) {
  try {
    // Replace YOUR-SUBGRAPH-URL with the url of your subgraph
    const SUBGRAPH_URL = "YOUR-SUBGRAPH-URL";
    const response = await axios.post(SUBGRAPH_URL, {
      query,
    });
    if (response.data.errors) {
      console.error(response.data.errors);
      throw new Error(`Error making subgraph query ${response.data.errors}`);
    }
    return response.data.data;
  } catch (error) {
    console.error(error);
    throw new Error(`Could not query the subgraph ${error.message}`);
  }
}
```

We will need to replace "**YOUR-SUBGRAPH-URL**" with the url of your subgraph&#x20;

* Go to the graph's [hosted service](https://thegraph.com/hosted-service)
* Click on **`My dashboard` ** and then on your graph.&#x20;
* Copy the url under **`Queries(HTTP)`**

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Update **index.js** into the **pages folder**

```javascript
import { BigNumber, Contract, ethers, providers, utils } from "ethers";
import Head from "next/head";
import React, { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";
import { abi, RANDOM_GAME_NFT_CONTRACT_ADDRESS } from "../constants";
import { FETCH_CREATED_GAME } from "../queries";
import styles from "../styles/Home.module.css";
import { subgraphQuery } from "../utils";

export default function Home() {
  const zero = BigNumber.from("0");
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // loading is set to true when we are waiting for a transaction to get mined
  const [loading, setLoading] = useState(false);
  // boolean to keep track of whether the current connected account is owner or not
  const [isOwner, setIsOwner] = useState(false);
  // entryFee is the ether required to enter a game
  const [entryFee, setEntryFee] = useState(zero);
  // maxPlayers is the max number of players that can play the game
  const [maxPlayers, setMaxPlayers] = useState(0);
  // Checks if a game started or not
  const [gameStarted, setGameStarted] = useState(false);
  // Players that joined the game
  const [players, setPlayers] = useState([]);
  // Winner of the game
  const [winner, setWinner] = useState();
  // Keep a track of all the logs for a given game
  const [logs, setLogs] = useState([]);
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();

  // This is used to force react to re render the page when we want to
  // in our case we will use force update to show new logs
  const forceUpdate = React.useReducer(() => ({}), {})[1];

  /*
    connectWallet: Connects the MetaMask wallet
  */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner();
      setWalletConnected(true);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * Returns a Provider or Signer object representing the Ethereum RPC with or without the
   * signing capabilities of metamask attached
   *
   * A `Provider` is needed to interact with the blockchain - reading transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being sent. Metamask exposes a Signer API to allow your website to
   * request signatures from the user using Signer functions.
   *
   * @param {*} needSigner - True if you need the signer, default false otherwise
   */
  const getProviderOrSigner = async (needSigner = false) => {
    // Connect to Metamask
    // Since we store `web3Modal` as a reference, we need to access the `current` value to get access to the underlying object
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    // If user is not connected to the Mumbai network, let them know and throw an error
    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 80001) {
      window.alert("Change the network to Mumbai");
      throw new Error("Change network to Mumbai");
    }

    if (needSigner) {
      const signer = web3Provider.getSigner();
      return signer;
    }
    return web3Provider;
  };

  /**
   * startGame: Is called by the owner to start the game
   */
  const startGame = async () => {
    try {
      // Get the signer from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const signer = await getProviderOrSigner(true);
      // We connect to the Contract using a signer because we want the owner to
      // sign the transaction
      const randomGameNFTContract = new Contract(
        RANDOM_GAME_NFT_CONTRACT_ADDRESS,
        abi,
        signer
      );
      setLoading(true);
      // call the startGame function from the contract
      const tx = await randomGameNFTContract.startGame(maxPlayers, entryFee);
      await tx.wait();
      setLoading(false);
    } catch (err) {
      console.error(err);
      setLoading(false);
    }
  };

  /**
   * startGame: Is called by a player to join the game
   */
  const joinGame = async () => {
    try {
      // Get the signer from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const signer = await getProviderOrSigner(true);
      // We connect to the Contract using a signer because we want the owner to
      // sign the transaction
      const randomGameNFTContract = new Contract(
        RANDOM_GAME_NFT_CONTRACT_ADDRESS,
        abi,
        signer
      );
      setLoading(true);
      // call the startGame function from the contract
      const tx = await randomGameNFTContract.joinGame({
        value: entryFee,
      });
      await tx.wait();
      setLoading(false);
    } catch (error) {
      console.error(error);
      setLoading(false);
    }
  };

  /**
   * checkIfGameStarted checks if the game has started or not and intializes the logs
   * for the game
   */
  const checkIfGameStarted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const randomGameNFTContract = new Contract(
        RANDOM_GAME_NFT_CONTRACT_ADDRESS,
        abi,
        provider
      );
      // read the gameStarted boolean from the contract
      const _gameStarted = await randomGameNFTContract.gameStarted();

      const _gameArray = await subgraphQuery(FETCH_CREATED_GAME());
      const _game = _gameArray.games[0];
      let _logs = [];
      // Initialize the logs array and query the graph for current gameID
      if (_gameStarted) {
        _logs = [`Game has started with ID: ${_game.id}`];
        if (_game.players && _game.players.length > 0) {
          _logs.push(
            `${_game.players.length} / ${_game.maxPlayers} already joined 👀 `
          );
          _game.players.forEach((player) => {
            _logs.push(`${player} joined 🏃‍♂️`);
          });
        }
        setEntryFee(BigNumber.from(_game.entryFee));
        setMaxPlayers(_game.maxPlayers);
      } else if (!gameStarted && _game.winner) {
        _logs = [
          `Last game has ended with ID: ${_game.id}`,
          `Winner is: ${_game.winner} 🎉 `,
          `Waiting for host to start new game....`,
        ];

        setWinner(_game.winner);
      }
      setLogs(_logs);
      setPlayers(_game.players);
      setGameStarted(_gameStarted);
      forceUpdate();
    } catch (error) {
      console.error(error);
    }
  };

  /**
   * getOwner: calls the contract to retrieve the owner
   */
  const getOwner = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const randomGameNFTContract = new Contract(
        RANDOM_GAME_NFT_CONTRACT_ADDRESS,
        abi,
        provider
      );
      // call the owner function from the contract
      const _owner = await randomGameNFTContract.owner();
      // We will get the signer now to extract the address of the currently connected MetaMask account
      const signer = await getProviderOrSigner(true);
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      if (address.toLowerCase() === _owner.toLowerCase()) {
        setIsOwner(true);
      }
    } catch (err) {
      console.error(err.message);
    }
  };

  // useEffects are used to react to changes in state of the website
  // The array at the end of function call represents what state changes will trigger this effect
  // In this case, whenever the value of `walletConnected` changes - this effect will be called
  useEffect(() => {
    // if wallet is not connected, create a new instance of Web3Modal and connect the MetaMask wallet
    if (!walletConnected) {
      // Assign the Web3Modal class to the reference object by setting it's `current` value
      // The `current` value is persisted throughout as long as this page is open
      web3ModalRef.current = new Web3Modal({
        network: "mumbai",
        providerOptions: {},
        disableInjectedProvider: false,
      });
      connectWallet();
      getOwner();
      checkIfGameStarted();
      setInterval(() => {
        checkIfGameStarted();
      }, 2000);
    }
  }, [walletConnected]);

  /*
    renderButton: Returns a button based on the state of the dapp
  */
  const renderButton = () => {
    // If wallet is not connected, return a button which allows them to connect their wllet
    if (!walletConnected) {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
    }

    // If we are currently waiting for something, return a loading button
    if (loading) {
      return <button className={styles.button}>Loading...</button>;
    }
    // Render when the game has started
    if (gameStarted) {
      if (players.length === maxPlayers) {
        return (
          <button className={styles.button} disabled>
            Choosing winner...
          </button>
        );
      }
      return (
        <div>
          <button className={styles.button} onClick={joinGame}>
            Join Game 🚀
          </button>
        </div>
      );
    }
    // Start the game
    if (isOwner && !gameStarted) {
      return (
        <div>
          <input
            type="number"
            className={styles.input}
            onChange={(e) => {
              // The user will enter the value in ether, we will need to convert
              // it to WEI using parseEther
              setEntryFee(
                e.target.value >= 0
                  ? utils.parseEther(e.target.value.toString())
                  : zero
              );
            }}
            placeholder="Entry Fee (ETH)"
          />
          <input
            type="number"
            className={styles.input}
            onChange={(e) => {
              // The user will enter the value for maximum players that can join the game
              setMaxPlayers(e.target.value ?? 0);
            }}
            placeholder="Max players"
          />
          <button className={styles.button} onClick={startGame}>
            Start Game 🚀
          </button>
        </div>
      );
    }
  };

  return (
    <div>
      <Head>
        <title>LW3Punks</title>
        <meta name="description" content="LW3Punks-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Random Winner Game!</h1>
          <div className={styles.description}>
            Its a lottery game where a winner is chosen at random and wins the
            entire lottery pool
          </div>
          {renderButton()}
          {logs &&
            logs.map((log, index) => (
              <div className={styles.log} key={index}>
                {log}
              </div>
            ))}
        </div>
        <div>
          <img className={styles.image} src="./randomWinner.png" />
        </div>
      </div>

      <footer className={styles.footer}>Made with &#10084; by Your Name</footer>
    </div>
  );
}
```

Create **index.js** in **my-app/constants** folder

<pre class="language-javascript"><code class="lang-javascript"><strong>// abi from hardhat_tutorial\artifacts\contracts\XXX.sol\XXX.json
</strong><strong>export const abi = [---your abi---];
</strong>export const RANDOM_GAME_NFT_CONTRACT_ADDRESS = 
 "address of your RandomWinnerGame contract";
</code></pre>

Run it from **my-app folder**

```shell
npm run dev
```

### Deploy our dApp

1. Go to [Vercel](https://vercel.com/) and sign in with your GitHub
2. Then click on `New Project` button and then select your RandomWinnerGame repo
3. When configuring your new project, Vercel will allow you to customize your `Root Directory`
4. Click `Edit` next to `Root Directory` and set it to `my-app`
5. Select the Framework as `Next.js`
6. Click `Deploy`




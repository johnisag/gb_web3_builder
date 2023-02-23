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

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### **Setting the Entities**&#x20;

* Open up you `subgraph.yaml` inside the `graph` folder
* Add a `startBlock` to the yaml file after the `abi: RamdomWinnerGame` line&#x20;
* To get the startBlock you will need to go to [Mumbai PolygonScan](https://mumbai.polygonscan.com/) and search up your contract address
* After that you will need to copy the block number of the block in which your contract was deployed

_The start block doesn't come with the default settings but because we know that we only need to track the events from the block the contract was deployed, we will not need to sync the entire blockchain but only the part after the contract was deployed for tracking the events_

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

### Query the data

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

****

****

****

****

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

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

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

_Note this is the abi of the `RandomWinnerGame` contract you created in the Chainlink VRF tutorial_

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

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### **Lets get started**

****

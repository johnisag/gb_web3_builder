---
description: Build sovereign user profiles using Ceramic Self.ID
---

# Using Ceramic Network

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* Ceramic is a decentralized data network, that allows for building composable Web3 applications.
* Ceramic decentralizes application databases,&#x20;
* Application developers can reuse data across applications and make them automatically interoperable.

### Web3 and Data

In the past few years, we have seen Web3 trends like DeFi, NFTs, and more recently DAOs blow up.

Smart contract platforms like Ethereum have shown us that dApps which act as legos, and can be composed together with other dApps to build entirely new dApps have a lot of value.

This is particularly highlighted with tokens that build on top of other tokens, DeFi protocols which utilize other DeFi protocols, etc.

<mark style="color:purple;">**Ceramic allows you to bring that same type of composability to data on the internet. This can be any kind of data. Profiles, social connections, blog posts, identities, reputation, game assets, etc.**</mark>

This is an abstract concept, and it is a bit tricky to try and define Ceramic Network as a single thing.

Similar to Ethereum, which by itself is hard to define (what does it even mean to be a smart contract plaform?), it is easier to understand if we look at specific use cases that Ceramic can enable and examples of a vision for the future.

Ethereum is much easier to understand when we look at exact examples like DeFi protocols, NFTs, DAOs etc - the same thinking can be applied while attempting to understand Ceramic.

### Lack of data in Web3

**The web3 market** right now is mainly comprised of financial applications.&#x20;

* Things to do with tokens and digital assets.
* &#x20;This is somewhat due to design.&#x20;
* Smart contracts are inherently limited in how much data can be stored in them, and there's only so much data functionality that can be built.

As we progress and dApps mature, the market demand for building more data-rich applications is increasing.&#x20;

Trends like decentralized social medias, decentralized reputation platforms, decentralized blogs, etc. is taking off, with a lot of technical approaches being taken, but lacking in one aspect or the other.

**In Web2**, these platforms were built as data silos.&#x20;

* Your Twitter posts and social connections are locked into the Twitter platform - you cannot transfer your social connections on Twitter to Facebook, you cannot transfer your Twitter posts to Facebook, etc.&#x20;
* They are all built as silos.

<mark style="color:purple;">**This will change. Ceramic is betting on interoperable applications and interoperable ecosystems being able to outcompete these siloed platforms.**</mark>

### What does Ceramic do?

Ceramic is building:

1. A generalized data protocol
2. where data can be modified only by the owner
3. with high volume data processing
4. with global data availability and consistency
5. with support for fast querying
6. with interoperable data across applications
7. and community governance

This is a lot. This is also why Ceramic can be tricky to define on it's own. After all, just like Ethereum, it is a generalized protocol, albeit for data.

To achieve the true scale and vision of Ceramic, a lot of breakthroughs need to be achieved.&#x20;

* If Ceramic is to become the decentralized database of the web, it **needs to be able to scale massively** - more than any centralized database today as none of them are storing data from the entire internet.
* **Data also needs to be made globally available**
* It needs to be ensured that **Ceramic node runners make that data available** for the rest of the world and **don't hijack it**.
* Ceramic also needs to be **fast to query and retrieve data from**
  * Users typically read much more data than they write, so fast reads and queries are extremely important for Ceramic to work at scale.
* All of this while **maintaining a high level of security and privacy** over data, and ensuring that it doesn't come crashing down one day.

It is helpful to look at specific use cases that can be enabled by Ceramic today and the benefits of having a mutable, decentralized, general purpose data protocol.

### Ceramic Use Cases

#### Decentralized Reputation

Reputation is highly tied into a person's identity.&#x20;

* On Twitter, it's followers and likes.&#x20;
* On Instagram, it is the hearts.&#x20;
* On Reddit, it's Karma, and&#x20;
* On StackOverflow, it's points.

In the web3 ecosystem today, **dApps can hardly do better** than centralized reputation systems like the above examples, with each platform having it's own reputation system.&#x20;

**This is largely because storing large amounts of decentralized data that can change over time was not viable.**&#x20;

And even if Ethereum Layer 2's were to reduce storage costs massively, what about non-Ethereum chains?&#x20;

What happens to your reputation when you switch to NEAR or Flow or Solana?

<mark style="color:purple;">Enter Ceramic.</mark>

* dApps can use Ceramic's data protocol to build standardized multi-chain reputation systems.&#x20;
* <mark style="color:orange;">A user can connect multiple wallets to their decentralized identity</mark>, belong from different blockchains, and data can be written to and updated from the user's decentralized data store on Ceramic.
* Regardless of what chain and dApp the user is using, they can carry around their reputation system with them.

#### Social Graphs

Similar to reputation, your social graph is also heavily centralized in today's world.&#x20;

* Twitter followers,&#x20;
* Facebook friends,&#x20;
* LinkedIn connections, and&#x20;
* Snapchat buddies are all differently siloed.

With **censorship increasing** on centralized social medias over time, and since social media companies are the biggest in the world with some of them having enough power to manipulate entire national elections, **the need for decentralized social media has never been more**.

Instead of locking users into a platform, we can actually allow for interoperability and optionality.

* dApps can follow standardized data models to store posts and social graphs.&#x20;
* These social graphs are carried by the user to whatever dApp they want to use.&#x20;
* &#x20;Products compete on which offers the best experience, not who has the most vendor lock-in.

This also allows for smaller players and startups with better products to achieve easier market penetration.&#x20;

They can utilize the underlying data that already exists, and when users sign up on their platform all their data is carried along with them.&#x20;

For bigger players, there can be financial incentives for providing a large amount of data for that datamodel.

#### Multi-Wallet and Multi-Chain Identity

You could extrapolate the decentralized reputation use case to achieve generalized multi-wallet and multi-chain identity for users.&#x20;

<mark style="color:purple;">Instead of tying up data in smart contracts or offchain based on wallet addresses, data can be tied up to a user's decentralized identity which can be controlled by multiple wallets across multiple chains.</mark>

This way, multi-chain dApps can have a decentralized, yet single, source of truth for a user's data.

### Multi Chain Architecture

At the lowest level, there is a decentralized identity. The most common approach to <mark style="color:orange;">decentralized identities on Ceramic is something called a</mark> <mark style="color:orange;"></mark><mark style="color:orange;">**3ID**</mark> <mark style="color:orange;"></mark><mark style="color:orange;"></mark> (Three ID) - named after the team behind Ceramic Network called 3Box Labs.

**A user can link multiple wallets from multiple chains to a single 3ID.**&#x20;

* Ceramic **supports** <mark style="color:purple;">more than 10 blockchains</mark>, and is continually adding support for more.
* 3IDs can own data on Ceramic Network.&#x20;

Data on Ceramic is referred to as **Streams**.&#x20;

* Each stream therefore **has an owner (or multiple owners)**.
* Streams have unique **StreamID**s, which remain the same over the lifetime of the stream.&#x20;
* **3IDs** can **modify** and **update** the **contents** of a **Stream** that they have **ownership on**.
* Streams have a **genesis** state, which is the initial data the Stream was created with.&#x20;
* Following the genesis state, users can create **commits** on Streams, which represent modifications to the data.&#x20;
* <mark style="color:orange;">The latest state of a Stream can be computed by starting from the genesis state and applying all the commits one by one.</mark>&#x20;
* The **latest state** is also referred to as the **tip** of a stream.

### Using Ceramic

Ceramic **provides** a suite of **high-level and low-level libraries and SDK's** to work with, depending on the use case.

**For common use cases**

* developers can use the **high-level SDK** (<mark style="color:purple;">**Self.ID**</mark>)&#x20;
* <mark style="color:purple;">**Self.ID**</mark> abstracts away most of the complexities of working with 3IDs and Streams.

**For complex or customized use cases**&#x20;

* developers can work with a **lower-level Ceramic HTTP Client**
* **lower-level Ceramic HTTP Client** connects to a **Ceramic node** they can run on their own (or the public nodes), and <mark style="color:purple;">**manage 3IDs and Stream data manually.**</mark>

For the purposes of this tutorial, we will stick with the Self.ID high-level SDK, as otherwise this tutorial will become extremely large.&#x20;

If you're interested in digging deeper, do check out their [documentation](https://developers.ceramic.network/learn/welcome/).

#### Self.ID

Self.ID is a single, high level, library that encapsulates&#x20;

* 3ID accounts, creating and setting up a 3ID,&#x20;
* underlying calls to Ceramic nodes,&#x20;
* all in a single package optimized to work in a browser.

The SDK also includes popular use cases like&#x20;

* multi-chain user profiles built in, which makes it very easy for developers to retrieve and store multi-chain data linked to a 3ID.

### Build with Ceramic

We will create a simple Next.js application, which uses Self.ID.&#x20;

* It will allow users to **login to the website using their wallet of choice**, which will be **linked** to **their 3ID**.&#x20;
* Then, we will **allow** the user to **write some data to their decentralized profile**, and be able to **retrieve it from the Ceramic Network**.
* For **verification** of this level, we will **ask** you to **enter your profile's StreamID at the end**.

**Bootstrap** the **Next js** project&#x20;

* Note use javascript project

```sh
npx create-next-app@latest ceramic-tutorial
```

Install **web3modal** and **ethers.js** libraries from within the project root

* Note : We install v5 specifically since the new v6 has breaking changes to the code.

```sh
npm install ethers@5 web3modal
```

Install the **Self.ID npm packages**, and a **dependent library** from within the project root

```sh
npm install "@self.id/react" "@self.id/web" key-did-provider-ed25519
```

**The first thing we need to do is **<mark style="color:purple;">**add Self.ID's Provider**</mark>** to the application.**&#x20;

The SDK exposes a **`Provider` ** component that **needs to be added to the root of your web app.**

* This initiatalizes the Self.ID instance,&#x20;
* connects to the Ceramic Network, and&#x20;
* makes Ceramic-related functionality available all across your app.

To do this, note in your **`pages` folder** Next.js automatically created a file called **`_app.js`.**&#x20;

* This is the root of your web-app, and&#x20;
* All other pages you create are rendered according to the configuration present in this file.&#x20;
* By default, **it does nothing special**, and just renders your page directly.&#x20;
* In our case, **we want to wrap every component of ours with the Self.ID provider.**

**Import the `Provider` from **<mark style="color:purple;">**Self.ID.**</mark>&#x20;

* Add the following line at the top of **`_app.js`**

```javascript
import { Provider } from "@self.id/react";
```

* Change the `MyApp` function in that file to return the `Component` wrapped inside a `Provider`

```javascript
function MyApp({ Component, pageProps }) {
  return (
    <Provider client={{ ceramic: "testnet-clay" }}>
      <Component {...pageProps} />;
    </Provider>
  );
}
```

* Run app to verify that everything is ok

```sh
npm run dev
```

Update **Home.modules.css** into the **styles folder**

```css
.main {
  min-height: 100vh;
}

.navbar {
  height: 10%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding-top: 1%;
  padding-bottom: 1%;
  padding-left: 2%;
  padding-right: 2%;
  background-color: orange;
}

.content {
  height: 80%;
  width: 100%;
  padding-left: 5%;
  padding-right: 5%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.flexCol {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.connection {
  margin-top: 2%;
}

.mt2 {
  margin-top: 2%;
}

.subtitle {
  font-size: 20px;
  font-weight: 400;
}

.title {
  font-size: 24px;
  font-weight: 500;
}

.button {
  background-color: azure;
  padding: 0.5%;
  border-radius: 10%;
  border: 0px;
  font-size: 16px;
  cursor: pointer;
}

.button:hover {
  background-color: beige;
}

```

Update **index.js** into the **pages folder**

<pre class="language-javascript" data-overflow="wrap"><code class="lang-javascript">import styles from "@/styles/Home.module.css";

import { Web3Provider } from "@ethersproject/providers";
//add hooks
import { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";

<strong>// Before we initialize Web3Modal, we will setup a React Hook provided to us by the // Self.ID SDK.
</strong>// Self.ID provides a hook called useViewerConnection which gives us an easy way to // connect
// and disconnect to the Ceramic Network.
import { useViewerConnection } from "@self.id/react";

// The last thing we need to be able to connect to the Ceramic Network is something // called an EthereumAuthProvider.
// It is a class exported by the Self.ID SDK which takes an Ethereum provider and an // address as an argument,
// and uses it to connect your Ethereum wallet to your 3ID.
import { EthereumAuthProvider } from "@self.id/web";

export default function Home() {
  // create a reference using the useRef react hook to a Web3Modal instance
  const web3ModalRef = useRef();

  // initialize React Hook provided to us by the Self.ID SDK
  const [connection, connect, disconnect] = useViewerConnection();

  // helper function to get the provider
  // This function will prompt the user to connect their Ethereum wallet, if not  
  // already connected, and then return a Web3Provider.
  // However, if you try running it right now, it will fail because we have not yet 
  // initialized web3Modal
  const getProvider = async () => {
    const provider = await web3ModalRef.current.connect();
    const wrappedProvider = new Web3Provider(provider);
    return wrappedProvider;
  };

  // We have seen this code before many times.
  // The only difference is the conditional here.
  // We are checking that if the user has not yet been connected to Ceramic,
  // we are going to initialize the web3Modal.
  useEffect(() => {
    if (connection.status !== "connected") {
      web3ModalRef.current = new Web3Modal({
        network: "goerli",
        providerOptions: {},
        disableInjectedProvider: false,
      });
    }
  }, [connection.status]);

  const connectToSelfID = async () => {
    const ethereumAuthProvider = await getEthereumAuthProvider();
    connect(ethereumAuthProvider);
  };

  // getEthereumAuthProvider creates an instance of the EthereumAuthProvider.
  // we are passing it wrappedProvider.provider instead of wrappedProvider directly
  // because ethers abstracts away the low level provider calls with helper functions
  // so it's easier for developers to use, but since not everyone uses ethers.js,
  // Self.ID maintains a generic interface to actual provider specification,
  // instead of the ethers wrapped version
  const getEthereumAuthProvider = async () => {
    const wrappedProvider = await getProvider();
    const signer = wrappedProvider.getSigner();
    const address = await signer.getAddress();
    return new EthereumAuthProvider(wrappedProvider.provider, address);
  };

  return (
    &#x3C;div className={styles.main}>
      &#x3C;div className={styles.navbar}>
        &#x3C;span className={styles.title}>Ceramic Demo&#x3C;/span>
        {connection.status === "connected" ? (
          &#x3C;span className={styles.subtitle}>Connected&#x3C;/span>
        ) : (
          &#x3C;button
            onClick={connectToSelfID}
            className={styles.button}
            disabled={connection.status === "connecting"}
          >
            Connect
          &#x3C;/button>
        )}
      &#x3C;/div>

      &#x3C;div className={styles.content}>
        &#x3C;div className={styles.connection}>
          {connection.status === "connected" ? (
            &#x3C;div>
              &#x3C;span className={styles.subtitle}>
                Your 3ID is {connection.selfID.id}
              &#x3C;/span>
              &#x3C;RecordSetter />
            &#x3C;/div>
          ) : (
            &#x3C;span className={styles.subtitle}>
              Connect with your wallet to access your 3ID
            &#x3C;/span>
          )}
        &#x3C;/div>
      &#x3C;/div>
    &#x3C;/div>
  );
}

// hook provided to us by Self.ID called useViewerRecord which allows storing and retrieving profile information on Ceramic Network.
import { useViewerRecord } from "@self.id/react";

//component to update our profile on ceramic network
function RecordSetter() {
  const record = useViewerRecord("basicProfile");
  const [name, setName] = useState("");

  const updateRecordName = async (name) => {
    await record.merge({
      name: name,
    });
  };

  return (
    &#x3C;div className={styles.content}>
      &#x3C;div className={styles.mt2}>
        {record.content ? (
          &#x3C;div className={styles.flexCol}>
            &#x3C;span className={styles.subtitle}>
              Hello {record.content.name}!
            &#x3C;/span>

            &#x3C;span>
              The above name was loaded from Ceramic Network. Try updating it
              below.
            &#x3C;/span>
          &#x3C;/div>
        ) : (
          &#x3C;span>
            You do not have a profile record attached to your 3ID. Create a
            basic profile by setting a name below.
          &#x3C;/span>
        )}
      &#x3C;/div>

      &#x3C;input
        type="text"
        placeholder="Name"
        value={name}
        onChange={(e) => setName(e.target.value)}
        className={styles.mt2}
      />
      &#x3C;button onClick={() => updateRecordName(name)}>Update&#x3C;/button>
    &#x3C;/div>
  );
}

</code></pre>

* Run app to verify that everything is ok

```sh
npm run dev
```

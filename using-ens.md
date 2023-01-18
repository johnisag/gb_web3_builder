---
description: The Ethereum Name Service
---

# Using ENS

### Background

When web initially started the only way you could explore information on the web was by entering in its IP address. After that the concept of DNS was introduced which helped us to link a domain name to an IP address.

So whenever you type `learnweb3.io`, DNS takes care of translating it to the respective IP which is what the computer finally understands.

### What is ENS?

ENS stands for `The Ethereum Name Service` and it behaves very similar to how DNS behaves in the web2 space. As we all know that Ethereum has long addresses which are hard to remember or type. ENS solves this issue by translating these wallet addresses, hashes etc into readable domains which are then saved on Ethereum blockchain.

The best part about ENS is unlike DNS servers which are centralized, ENS works with the help of a smart contract which is censorship resistant. So now when you are sending your wallet address to someone which looks like `0x1234huiahi....` you can actually send them `tom.eth` and the ENS would figure out that `tom.eth` is actually equal to your wallet address (`0x1234huiahi....`)

Additionally, ENS extends beyond just mapping wallet addresses to human-readable names. You can actually attach a profile picture, a description, social media links, as well as any custom types of data you'd want to attach.

### Requirements

Its time to build something where we can use ENS. We will develop a website which can display the ENS for an address if it has one.

### Setup an ENS

First let's get an ENS name for your address, start by opening up [https://app.ens.domains/](https://app.ens.domains/)

* Connect to **Goerli Testnet**
* Also have some **Goerli Ether**

Click on **`Available`**

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Then click on **`Request To Register`**

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

When the progress bar enters the 3rd step, Click ** `Register`**

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Then after the progress bar finishes click on **`Set As Primary ENS Name`**

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

From the dropdown then select the ENS name you just created

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Click **`Save`**

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Now you have an ENS registered to your address on Goerli.

### dApp&#x20;

**Bootstrap** the **Next js** project from the project root and run it (**http://localhost:3000**)

* Note use javascipt project

```shell
npx create-next-app@latest
cd my-app
npm run dev
```

Install **web3modal** and **ethers.js** libraries

```shell
npm install web3modal
npm install ethers

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

.image {
  width: 70%;
  height: 50%;
  margin-left: 20%;
}

.title {
  font-size: 2rem;
  margin: 2rem 0;
}

.description {
  line-height: 1;
  margin: 2rem 0;
  font-size: 1.2rem;
}

.button {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 20px;
  width: 200px;
  cursor: pointer;
  margin-bottom: 2%;
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

Update **index.js** into the **pages folder**

```javascript
import Head from "next/head";
import styles from "../styles/Home.module.css";
import Web3Modal from "web3modal";
import { ethers, providers } from "ethers";
import { useEffect, useRef, useState } from "react";

export default function Home() {
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();
  // ENS
  const [ens, setENS] = useState("");
  // Save the address of the currently connected account
  const [address, setAddress] = useState("");

  /**
   * Sets the ENS, if the current connected address has an associated ENS or else it sets
   * the address of the connected account
   */
  const setENSOrAddress = async (address, web3Provider) => {
    // Lookup the ENS related to the given address
    var _ens = await web3Provider.lookupAddress(address);
    // If the address has an ENS set the ENS or else just set the address
    if (_ens) {
      setENS(_ens);
    } else {
      setAddress(address);
    }
  };

  /**
   * A `Provider` is needed to interact with the blockchain - reading transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being sent. Metamask exposes a Signer API to allow your website to
   * request signatures from the user using Signer functions.
   */
  const getProviderOrSigner = async () => {
    // Connect to Metamask
    // Since we store `web3Modal` as a reference, we need to access the `current` value to get access to the underlying object
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    // If user is not connected to the Goerli network, let them know and throw an error
    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 5) {
      window.alert("Change the network to Goerli");
      throw new Error("Change network to Goerli");
    }
    const signer = web3Provider.getSigner();
    // Get the address associated to the signer which is connected to  MetaMask
    const address = await signer.getAddress();
    // Calls the function to set the ENS or Address
    await setENSOrAddress(address, web3Provider);
    return signer;
  };

  /*
    connectWallet: Connects the MetaMask wallet
  */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner(true);
      setWalletConnected(true);
    } catch (err) {
      console.error(err);
    }
  };

  /*
    renderButton: Returns a button based on the state of the dapp
  */
  const renderButton = () => {
    if (walletConnected) {
      <div>Wallet connected</div>;
    } else {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
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
        network: "goerli",
        providerOptions: {},
        disableInjectedProvider: false,
      });
      connectWallet();
    }
  }, [walletConnected]);

  return (
    <div>
      <Head>
        <title>ENS Dapp</title>
        <meta name="description" content="ENS-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>
            Welcome to LearnWeb3 Punks {ens ? ens : address}!
          </h1>
          <div className={styles.description}>
            Its an NFT collection for LearnWeb3 Punks.
          </div>
          {renderButton()}
        </div>
        <div>
          <img className={styles.image} src="./learnweb3punks.png" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by LearnWeb3 Punks
      </footer>
    </div>
  );
}

```

Run it from **my-app folder**

```shell
npm run dev
```

### dApp Deploy

* Go to [Vercel](https://vercel.com/) and sign in with your GitHub
* Then click on `New Project` button and then select your NES repo
* When configuring your new project, Vercel will allow you to customize your `Root Directory`
* Click `Edit` next to `Root Directory` and set it to `my-app`
* Select the Framework as `Next.js`
* Click `Deploy`

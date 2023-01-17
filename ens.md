---
description: The Ethereum Name Service
---

# ENS

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

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Now you have an ENS registered to your address on Goerli.








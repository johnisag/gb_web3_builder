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

### Ceramic's Multi Chain Architecture

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






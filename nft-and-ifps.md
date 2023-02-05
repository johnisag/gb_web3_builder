---
description: Create an NFT Collection with metadata stored on IPFS
---

# NFT and IFPS

### Requirements

* There should only exist 10 LearnWeb3 Punk NFT's and each one of them should be unique.
* User's should be able to mint only 1 NFT with one transaction.
* The metadata for the NFT's should be stored on IPFS
* There should be a website for your NFT Collection.
* The NFT contract should be deployed on Mumbai testnet

### IPFS

* We need to upload our images and metadata on to IPFS.&#x20;
* We'll use a service called [Pinata](https://www.pinata.cloud/) which will help us upload and pin content on IPFS.&#x20;
* Once you've signed up, go to the [Pinata Dashboard](https://app.pinata.cloud/pinmanager) and click on 'Upload' and then on 'Folder'.
* Download [the LW3Punks folder](https://github.com/LearnWeb3DAO/IPFS-Practical/tree/master/my-app/public/LW3punks) to your computer and then upload to it `Pinata`, name the folder `LW3Punks`

Now you should be able to see a `CID` for your folder

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* You can check that it actually got uploaded to IPFS is by opening this up: `https://ipfs.io/ipfs/your-nft-folder-cid` replace `your-nft-folder-cid` with the CID you recieved from pinata.
* The images for your NFT's have now been uploaded to IPFS but just having images is not enough, each NFT should also have associated metadata
* We will now upload metadata for each NFT to IPFS, each metadata file will be a `json` file. Example for metadata of NFT `1` has been given below:

```json
{
  "name": "1",
  "description": "NFT Collection for LearnWeb3 Students",
  "image": "ipfs://QmQBHarz2WFczTjz5GnhjHrbUPDnB48W5BM2v2h6HbE1rZ/1.png"
}
```

Note how "image" has ipfs location in it instead of an `https` url. Also note that because you uploaded a folder, you will also need to specify which file within the folder has the correct image for the given NFT. Thus in our case the correct way to specify the location for an NFT image would be `ipfs://CID-OF-THE-LW3Punks-Folder/NFT-NAME.png`

We have pre-generated files for metadata for you, you can download them to your computer from [here](https://github.com/LearnWeb3DAO/IPFS-Practical/tree/master/my-app/public/metadata), upload these files to pinata and name the folder `metadata`

Now each NFT's metadata has been uploaded to IPFS and pinata should have generated a CID for your metadata folder

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

You can check that it actually got uploaded to IPFS is by opening this up: `https://ipfs.io/ipfs/your-metadata-folder-cid` replace `your-metadata-folder-cid` with the CID you recieved from pinata.

Copy this CID and store it on your notepad, you will need this futher down in the tutorial

### Contract

We will be using [**`Ownable.sol`**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) from Openzeppelin to manage the **`Ownership`** of a contract

* By default, the owner of an **Ownable** contract is the account that deployed it, which is usually exactly what you want.
* **Ownable** also **allows** you to:
  * **transferOwnership** from the **owner account to a new one**, and
  * **renounceOwnership** for the owner to relinquish this administrative privilege, a common pattern after an initial stage with centralized administration is over.

We will also be using an extension of ERC721 known as [**ERC721 Enumerable**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)

* **ERC721 Enumerable** helps you to **keep track of all the tokenIds** in the contract **and** also the **tokensIds held by an address** for a given contract.
* Please have a look at the [**functions**](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721Enumerable) it implements before moving ahead

To build the smart contract we would be using [**Hardhat**](https://hardhat.org/).

* Hardhat is an **Ethereum development environment and framework** designed for full stack development in Solidity.
* You can **write** your smart contract, **deploy** them, **run tests**, and **debug** your code.

**Setup a Hardhat project**

```shell
mkdir nft-ipfs
cd nft-ipfs
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

# bootstrap the hardhat project
npx hardhat
```








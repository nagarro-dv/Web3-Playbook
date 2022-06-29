# Truffle suite development stack

This section looks at a project using the truffle suite, web3.js, @truffle/contract.js and the Metamask browser wallet.

Where possible, in most cases we point to the official quick start documentation for the different tools as this will be better updated and future-proof. 

Therefore, in essence this walk through will be more of a pointer to where to look and read to get up and running. 


### Truffle
The [truffle suite](https://trufflesuite.com/) is a collections of tools for local dApp (Distributed Application) development framework for Ethereum and essentially consists of three different parts, Truffle, Ganache, and Drizzle.

We look at Truffle and Ganache. 

We look at how to configure the FE so it communicates with local and testnet nodes via metamask

### Truffle quickstart

The truffle documentation is a good place to start and get up and running. To familiarise yourself with truffle you should follow the quickstart guide.

https://trufflesuite.com/docs/truffle/quickstart.html

The tutorial uses a simple Faucet app [Download Faucet app](https://github.com/zakcroft/solidity-blockchain-Faucet), but you should check the truffle site for preconfigure projects called "boxes"".

Boxes are preconfigured bootstrap projects for different frameworks and libraries.

A Faucet in this sense is a simple contract that allows adding and withdrawing ETH.

This will familiarise you with the commands **truffle compile**, **truffle develop** and **truffle migrate**. 

Let's look at the smart contract
- [Faucet.sol smart contract](https://github.com/zakcroft/solidity-blockchain-Faucet/blob/master/contracts/Faucet.sol)

### Truffle compile
Run ```truffle compile```

After you compile you can see a new folder is created called **build/contract**. These are the **Artifacts** that are stored in JSON format. 

These files hold the ABIs for the contracts in JSON format. 

The ABI is the Application Binary Interface the FE uses to communicate with the compiled contract. 

It lays out the schema for the available functions, parameters and state variable that can be used to interface with the Smart Contract.

It also has a copy of the compiled **bytecode** and **deployedBytecode** that is deployed to the blockchain.

- [Artifacts](https://trufflesuite.github.io/artifact-updates/background.html#what-are-artifacts)
- [JSON ABI Spec](https://docs.soliditylang.org/en/develop/abi-spec.html#json)
- [Contract-schema](https://github.com/trufflesuite/truffle/tree/develop/packages/contract-schema)

### Truffle develop
You will see that "truffle develop" gives you an in memory blockchain for developing with on PORT 9545. 

This is an interactive console that also spawns a development blockchain. 

|         ![ScreenShot](../img/2-truffle/inmemory-blockchain.png)        |
|:--------------------------------------------------------------------------------------:|
|            *Source: https://trufflesuite.com/docs/truffle/quickstart.html/*            |

It creates the Mnemonic seed-phrase for the accounts, derives the private keys and the EOA account addresses.

### Truffle migrate
When you migrate with the command **truffle migrate** or just **migrate** as you are in the truffle develop console, you deploy the compiled contract's bytecode to the blockchain. 

By default truffle uses the first account's address as the sender(creator or owner) and a new contract address is created with the contract bytecode.

|                ![ScreenShot](../img/2-truffle/migrate.png)                 |
|:----------------------------------------------------------------:|
| *Source: https://trufflesuite.com/docs/truffle/quickstart.html/* |

See Deploying 'Faucet' in the console.

```account: 0x627306090abab3a6e1400e9345bc60c78a8bef57```

and 

```contract address:    0x6891Ac4E2EF3dA9bc88C96fEDbC9eA4d6D88F768```

If you have not already done so, while in the develop console try some other commands to interact with the contract. 

##### Commands to interact with the contract
```
let instance = await Faucet.deployed()

instance.address

instance.send("1000000000000000000")

// we have an instance of web3 in the console which is covered later
// but web3 is a utility package for Ethereum.

await web3.eth.getBalance(instance.address)

await instance.addFunds({value:"1000000000000000000"})

// get the balance again
await web3.eth.getBalance(instance.address)

let fundersBalance = await instance.getFundersBalance()
// returns a BigNumber

// convert to string
fundersBalance.toString()
```

### Truffle console and Ganache
There is a second console that is used if you want to connect to a blockchain that is not in memory. 

This is the preferred way, and you will want this when connected to Ganache and testnets.

First we connect to Ganache. Ganache is another local development blockchain that comes with a UI. 

If you have not done so follow installing and configuring ganche. 

- [Using Ganache](https://trufflesuite.com/docs/truffle/quickstart.html#alternative-migrating-with-ganache)

Double-check the PORT number in Ganache server settings matches your development PORT in truffle.config.js. 

You will now be able to run the **truffle console** command.

This will look for a network definition called development in the network's configuration in truffle.config.js, and connect to it, if available. 

You should now be connected to the Ganache dev blockchain.

Run the command ```accounts``` in the console and see the accounts[0] is the same as accounts[0] in the Ganache UI.

![ScreenShot](../img/2-truffle/ganache.png)                 

Notice the balance has 100.00 ETH

Run ```migrate``` in the console

Now go to contract tab in Ganache and see there is a contract created.

![ScreenShot](../img/2-truffle/Contract-created-2.png)

You can now run the same commands and see the changes on Ganache.

[Commands to interact with the contract](#commands-to-interact-with-the-contract)

##### Parse a transactions
Also, you can query the transaction

Click on one, and you will see the TX DATA field. This is the Transaction data.

![ScreenShot](../img/2-truffle/contract-creations.png)

Run ```web3.eth.getTransaction("TRANSACTION_ID")``` in the console

You will see this decodes transaction. 

The input field holds, amongst other things, the function identifiers, parameters and the contract code in HEX.

See how account[0] now has <=99.99 ETH after making the transaction due to gas cost.

The next step is to configure the metamask wallet to use the same network as Ganache. 


### Install Metamask
Install metamask and use the seed phrase it creates for you. Always keep a separate dev seed phrase to any personal seed phrases. 

Also make sure you install the correct metamask wallet, there are impersonating wallets out there.

- [Install metamask ](https://metamask.io/)

Add a custom network. 
- [Add custom network](https://metamask.zendesk.com/hc/en-us/articles/360043227612-How-to-add-a-custom-network-RPCl)

Make sure to use the Ganache settings.

![ScreenShot](../img/2-truffle/ganach-metamask.png)                 

Now you will be able to switch to the Ganache network in metamask, but you will notice you are not seeing any Ganache account.

The accounts in metamask are the accounts made from the seed phrase you used when you installed metamask. 

Ganache has its own set of accounts from it's own generated seed phrase.

In Ganache click on show keys for the first address and copy the private key.

|                 ![ScreenShot](../img/2-truffle/show-keys.png)                  |
|:----------------------------------------------------------------:|


While switched to the Ganache network in metamask import this into metamask. 

- [Import account to metamask](https://metamask.zendesk.com/hc/en-us/articles/360015489331-How-to-import-an-Account)

You should now see you first Ganache account in metamask with about 99.99 ETH

You are now ready to communicate from the Browser to the Ganache blockchain. 


### Connect to a testnet

To connect to a testnet in truffle you need to alter you truffle-config.js and use HDWalletProvider.js. 
HD means (Hierarchical Deterministic (HD) Wallet) - Basically uses seed phrases.

You also need a client node to use for your access point to the blockchain, so we use the free node as a service https://infura.io/.
Go to https://infura.io/ and sign up for a free account and configure a new project.

You will also need some test ETH in your wallet, if you have not done so install metamask, switch to a testnet and get some test ETH.
There are many outlets/Faucets to get this. See a few below.

- https://ropsten.oregonctf.org/
- https://goerlifaucet.com/
- https://faucet.metamask.io/

Install the truffle HDWalletProvider and follow the installation instructions to configure your truffle config.

- [Install HDWalletProvider](https://github.com/trufflesuite/truffle/tree/master/packages/hdwallet-provider#readme)

Example 

```provider: () => new HDWalletProvider(process.env.MNEMONIC, "https://ropsten.infura.io/v3/<project-id>"),```

In this example the MNEMONIC is the seed phrase for the wallet you are using for development and is stored outside or any git commits to keep it private.
The url is the client node that will give us an access point to the blockchain network. Add the correct testnet node endpoint from infura.io.

Once you have this set up run ```truffle console  --network <name>``` and then ```migrate```. The Smart contract will be deployed to the testnet.

### Web3 Providers.

Notice how HDWalletProvider is called a provider.
Providers are used as a way to provide to communicate with a client node with JSON-RPC requests

They can be programmatic or in the case of the browser a JS object.

However, ultimately a provider provides a way to communicate over a protocol like http or ws with a client node using JSON-RPC.

Providers are then wrapped in further functionality that gives additional methods and functionality.

```HDWalletProvider``` is programmatic and created private keys from the seed phrase and signs transactions for you.
In that way you can deploy smart contracts to the blockchain from running commands and code. You don't need a software wallet.

```Metamask``` on the other hand injects a JS object into the browser and is available at `window.ethereum`.
This is used on the FE to interface with the wallet so the user can control signing transactions with the wallet.  

Definitions
- Provider:
A JavaScript object made available to a consumer, that provides access to Ethereum by means of a Client.
- Client:
An endpoint that receives Remote Procedure Call (RPC) requests from the Provider, and returns their results.
- Wallet:
An end-user application that manages private keys, performs signing operations, and acts as a middleware between the Provider and the Client.
- Remote Procedure Call (RPC):
A Remote Procedure Call (RPC), is any request submitted to a Provider for some procedure that is to be processed by a Provider, its Wallet, or its Client.

We can now look at the FE and the windows.ethereum object and associated libraries. 

Overview

|                  ![ScreenShot](../img/2-truffle/Truffle-providor-overview.png)                 |
|:----------------------------------------------------------------:|

### Resources
- [Truffle suite](https://trufflesuite.com/index.html)
- [Truffle console](https://trufflesuite.com/docs/truffle/getting-started/using-truffle-develop-and-the-console#console)
- [Why is there a truffle develop console and truffle console](https://trufflesuite.com/docs/truffle/getting-started/using-truffle-develop-and-the-console#why-two-different-consoles)
- [Truffle quick start](https://trufflesuite.com/docs/truffle/quickstart.html)
- [Truffle boxes](https://trufflesuite.com/boxes/index.html)

Other dev environments
- [Hardhat](https://hardhat.org/)



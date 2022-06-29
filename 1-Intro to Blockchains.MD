# Intro to Blockchains and the Ethereum Virtual Machine (EVM).

The purpose of these pages is to outline some options for Web3/blockchain development on the Ethereum Blockchain with the Ethereum Virtual Machine (EVM).

Although this document is from a FE perspective, there are fundamentals concepts related to blockchains and smart contract code you will need to be familiar with. 

So it should serve the following top level goals:

1. You want to understand the basics of a blockchain and EVM development so you can interface with a blockchain or smart contract from a FE application .
2. You are starting a FE project and want to understand how to get a project up and running and want a walk-through of the basics.
3. You want to understand the main present options available for your development stack and recommended available libraries.

Some things to be aware of:
1. This is not a best practices document and will look at current dev environment stacks.
2. This is a technical overview and aims to cover the basics.  
3. This does not cover how a blockchains cryptographically functions. For this see the recommended reading at the end of this document.

## Blockchain basics
In order to understand how to interface with a blockchain we need some background.

###  What is a blockchain.
A blockchain is a distributed ledger run on many geographically different nodes that hold a copy of the blockchain and use a consensus mechanism protocol to confirm and validate new entries onto the ledger. 

|                       ![ScreenShot](../img/nodes.jpeg)                        |
|:--------------------------------------------------------------------:|
| *Source: https://www.coindesk.com/markets/2014/05/09/what-are-bitcoin-nodes-and-why-do-we-need-them/* |

Breaking this down.
 - Distributed ledger - The ledger is the blockchain
 - Nodes - These are client nodes anyone can run
 - Copy of the blockchain - Each node has one.
 - Consensus mechanism protocol - The way all node agree by consensus the next entry is valid
 - Entries - These are new blocks that hold transactions


These entries are blocks of transactions that periodically get added to the end of the ledger that record a historical immutable chain of all transactions. 

This chain is cryptographically secured where the amount of effort to unwind the chain and rewrite it is computationally expensive and on some protocols would lead to slashing (a type of fine). 

Tokens, also known as digital assets or cryptocurrencies form the basis of these transactions and therefore transact value between account or smart contract addresses.

The Ethereum blockchains token is Ether, normally referred to as ETH. 

|                        ![ScreenShot](../img/ledger.diagram.png)                        |
|:--------------------------------------------------------------------------------------:|
| *Source: https://hyperledger-fabric.readthedocs.io/en/release-2.2/ledger/ledger.html*  |

Consensus mechanisms can include Proof of Work and Proof of Stake. See link below for further reading. 

Fundamentally these consensus mechanisms are how **miners** go about validating and adding blocks to the blockchain in return for rewards (newly minted tokens).

|                                ![ScreenShot](../img/mining.png)                                |
|:-------------------------------------------------------------------------------------:|
| *Source: https://hyperledger-fabric.readthedocs.io/en/release-2.2/ledger/ledger.html* |

There are many blockchains that use different consensus mechanisms, protocols, coding languages and compilers/virtual machines.

Some are private but most are public which means anyone can see all the history of all the transactions.

Primarily we will focus on the Ethereum blockchain, Solidity coding language and the Ethereum Virtual Machine,
however it should be noted that the Rust coding language is also becoming very popular in this field. 

The Ethereum Virtual Machine (EVM) runs EVM bytecode that is created by compiling another higher level language.
The most popular and frequently used is Solidity and has similar syntax to JavaScript, C++ and Java.  

You can browse the Ethereum Blockchain on Etherscan, link below. 

- [Types of Blockchains POW, POS and Private](https://www.gemini.com/cryptopedia/blockchain-types-pow-pos-private)
- [What is Ethereum](https://ethereum.org/en/what-is-ethereum/)
- [Consensus mechanisms](https://ethereum.org/en/developers/docs/consensus-mechanisms/)
- [Etherscan](https://etherscan.io/)
- [Solidity](https://docs.soliditylang.org/en/v0.8.11/)

### Client nodes.
Distributed nodes also called client nodes are full node Ethereum protocol implementations written in many languages like Go, Rust, JS. 
You can also have light nodes and archive node. See links below.

Client nodes include an EVM and a copy of both the global Ethereum state and the transaction memory-pool. 

Ethereum's state is a large data structure which holds all accounts and balances and a machine state which is used in the execution of arbitrary machine code.

|              ![ScreenShot](../img/client-diagram.png)                 |
|:--------------------------------------------------------------------:|
| *Source: https://ethereum.org/en/developers/docs/nodes-and-clients/* |

All implement a uniform set of endpoints from the Ethereum JSON-RPC specification which is transport agnostic and the format can be used over websockets or HTTP and just uses JSON as a data format.

Therefore, if you want to communicate with the blockchain you need to make JSON-RPC calls to any single node and if they are updates then these will be propagated to the other nodes and any updates verified by consensus.

If they are not updates they are just reads and no propagations is needed. The data is returned because the node has a copy of this information.

- [Client node types](https://ethereum.org/en/developers/docs/nodes-and-clients/#node-types)

### Networks
Ethereum's main blockchain is called main-net and is where all real transactions take place.
For testing purposes there are test-nets that share the same addresses as main-net, as seen in metamask in the following image.

|                                        ![ScreenShot](../img/test-nets.jpeg)                                        |
|:-----------------------------------------------------------------------------------------------------------------:|
| [Source medium.com](https://medium.com/compound-finance/the-beginners-guide-to-using-an-ethereum-test-network-95bbbc85fc1d) |

These may need to be switched on in wallet settings.

This means your wallets will use the same address and private keys across all main and test networks, but balances and transactions will be different.

It is recommended you use a dedicated seed phrase(see wallet section) for development so not to send real ETH accidentally.

Some test-nets are Rinkeby, Kovan, Ropsten and Goerli and you can see the similarities and differences in the following urls.

* https://etherscan.io/address/0x7c87248fe069d4ad4cddc235d39449f7aae812c0
* https://rinkeby.etherscan.io/address/0x7c87248fe069d4ad4cddc235d39449f7aae812c0
* https://ropsten.etherscan.io/address/0x7c87248fe069d4ad4cddc235d39449f7aae812c0

### Addresses, smart contracts and state.
Ethereum's addresses are either Externally Owned Addresses EOA's or Smart Contracts addresses.

EOA have associated private keys and are used in wallets to sign the transaction. 

Smart Contract addresses don't have private keys available and receive and make transaction via the code.

Both EOA's and Smart Contract have the fields nonce and balance, but the Smart Contract also has fields for code and data.
- Nonce is an integer that is incremented every time the address sends any transaction.
- Balance is the address balance it ETH.
- Code holds code surprising and is really made up of the all the functions.
- Data holds the Smart contract's state and is made up of all the state variables.

|                        ![ScreenShot](../img/Addresses.png)                        |
|:--------------------------------------------------------------------:|
| *Source: https://blog.gnosis.pm/breaking-down-ethereum-wallets-options-2162b41477d7* |


The point to note is that you can send ETH to and from both types of addresses, and it will increase or decrease the address balance.

But EOAs use the private keys to transact and Smart contracts are already at the address, so they can just transact from the code.

Smart contract also have the added benefit that they can run code to do many other tasks.

A deeper understanding of smart contracts can be seen in the lnk below. 

- [Smart Contract](https://ethereum.org/en/developers/docs/smart-contracts/anatomy/)
- [EOAs and Smart Contract addresses](https://www.youtube.com/watch?v=9_qoGDaYI4w)
- [EVM storage](https://coinyuppie.com/in-depth-understanding-of-evm-storage-mechanism-and-security-issues/)
- [An EOA address with no code field](https://etherscan.io/address/0x175a43757d91fc81c7e756d706ea58a2a0d1a52a)
- [A smart contract address with code field](https://etherscan.io/address/0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae#code)

## Smart contract security.
Smart contract security is a very deep discussion but a top level tip is you should be using a tried and tested library to be building off like openzeppelin.
Don't try to write them yourself as deeper security implementation have been taken care of for you.

- [Openzeppelin smart contract library](https://docs.openzeppelin.com/contracts/4.x/)
- [Rug pulls, scams and smart contract exploitations](https://rekt.news/)

## Transactions and Gas
Transaction on Ethereum cost ETH and you are charged for making transaction or to execute contract code that may result in a state update.
Reading from the blockchain is free.

1 Ether, normally referred to as **ETH** can be split into 10<sup>18</sup> wei, where wei is the lowest denomination of ETH.

Therefore, 10<sup>9</sup> wei is a giga wei or nanoether or the more used term is gwei. Transactions costs are calculated in **gwei**.

**Gas** is the unit of measurement used in calculating the cost of transactions.

Note that gas is not ETH, it is a separate unit of measurement.

On every new block a **base fee** is set and is the minimum price(in gwei) per unit of gas for that block. Tips can also be included for the miners.

Adding up the (total gas used x base fee) gives you the total cost of the transaction.

So if you use up 200,000 gas units and the base fee is 100 gwei then the cost is 200,000 gas * 100 gwei per gas = 20,000,000 gwei ~ 0.02 ETH.

However, an EOA to EOA transaction is a straight exchange of ETH from balance to balance and has a standard gas fee set to 21,000 Gas.

Smart contract executions vary due to the difference in the code execution of the EVM OP Codes used when running the code.

If the state update fails you are still charged the fee as the EVM instructions would have run.

For development purposes you will be on a local blockchain or a testnet so gas costs nothing as you will have test ETH in your wallet.

However, you should look at gas costs if you are writing the smart contracts because the way they are executed directly affects gas costs on the final user.

- [Gas](https://ethereum.org/en/developers/docs/gas/)
- [Gas price tracker](https://ethgasstation.info/)
- [OP code Gas prices](https://ethereum.org/en/developers/docs/evm/opcodes/)

## Wallets
Wallets connect to the blockchain, read the data and can make transactions for you at a click of a button.

They do not hold any currency like a pocket wallet. They read the balance and display from the blockchain which is public data available to everyone.

To make transactions you need to sign the transaction with your private key, which is stored in the wallet.

These private keys are derived from your seed phrase which is also in you wallet. The seed phrase is basically your master password.

Example:
```
Mnemonic: promote nurse foster unique oppose pill cram climb practice trial angle paper

Private Keys:
(0) b7b5af1e9e8017b49a941a022a2d566b9b11a8340c916b1158cc94ed4d933c13
(1) c0b7c27cdd9c74e1f62a1b8a4a991275f61420180aaaf5d42950779ef54d779c
(2) 97499e72dd2b2f065c01f6b7b25373803c0db40ed38fb501971792f9a885b305
(3) e6420688705107e43f5737cd91672c5554572b37d6f3f188f0d0596b5f3eb11e
...

Accounts:
(0) 0x41f030214d5542d681654f79435995e9f7acb7e4
(1) 0x69583df865d2cf2360883de539abf7d0a03a2b2f
(2) 0x5d45b602adf36dc195d3e74e188ae2d0026d65e2
(3) 0xa59ac139bd8f2378b325069a49d5914c5ecbeddc
...
```

Seed phrases make many private keys and each account has a private key associated with it.

It is recommended you use a dedicated seed phrase for development so not to send real ETH accidentally.

There are a few type of wallets that are used to hold your seed phrases and private keys.
They are Hot wallets, Hardware wallets and Paper wallets. Also, some are custodial and some are non-Custodial.

Custodial wallets are where some other entity stores your seed phrases or private keys. This more often is something like an exchange.

Non-Custodial wallets are where you personally take responsibility for storing and the security of your seed phrases or private keys.

- Hot wallets are non-Custodial software wallets connected to the internet.
  You store your seed phrase in the wallet, which can derive you private keys and is then able to transact for you using the private keys.
  Hot wallets have security implication as the seed phrase and private keys can be hacked and stolen due to being on a device connected to the internet.
  There are many types of Hot wallets. Some are chain specific like Metamask(Ethereum, Polygon) and Phantom(Solana).
  Others can deal with multiple chains like Trust wallet (Mobile wallet).

- Hardware wallets (Cold wallets) are non-Custodial and store the seed phrases on a hardware device not connected to the internet, thus removing the security issue. You only connect to a computer when you want to make a transaction.

- Paper wallets: Some people keep a written version, but it is not recommended.


You should look at integrating both Hot wallets and Hardware wallet as both are mainstream.

However, for general development you should just use a Hot wallet. We will use the MetaMask browser extension.

- [Metamask](https://metamask.io/)
- [Trust wallet](https://trustwallet.com/)
- [Seed phrases](https://www.coinbase.com/learn/crypto-basics/what-is-a-seed-phrase)
- [Seed phrases video](https://www.youtube.com/watch?v=wWCIQFNf_8g)

### Recommended reading
- [Mastering Bitcoin on github](https://github.com/bitcoinbook/bitcoinbook)
- [Mastering Bitcoin paperback](https://www.amazon.co.uk/Mastering-Bitcoin-2e-Andreas-Antonopoulos/dp/1491954388)
- [Mastering Ethereum on github](https://github.com/ethereumbook/ethereumbook)
- [Mastering Ethereum paperback](https://github.com/ethereumbook/ethereumbook)

Summary Diagram
          ![ScreenShot](../img/Summary.png)                        



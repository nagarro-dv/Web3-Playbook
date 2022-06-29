## FE Set Up
This section covers how the FE application communicates with the wallet on to the client node and the deployed smart contract. 

Let look at the [FE code.](https://github.com/zakcroft/solidity-blockchain-Faucet/blob/master/src/App.js)

We use Metamask as this is a popular option but there are other wallets.

First we need to detect the provider and the user has the wallet installed.

JS providers follow the [eip-1193](https://eips.ethereum.org/EIPS/eip-1193) standard so your app should run with different ethereum wallets if they follow the standard.

However, historically, providers have been made available as ```window.ethereum``` in web browsers, but this convention is not part of the specification.

In this case the provider is the ```window.ethereum``` object. This is injected into the browser by Metamask. 

### Detect provider loaded
To detect the metamask injected ethereum provider has loaded use the following library to check and gain access to it.

```
import detectEthereumProvider from "@metamask/detect-provider"

const provider = await detectEthereumProvider();

if (provider) {
  // From now on, this should always be true:
  // provider === window.ethereum
  
  initApp(provider); // initialize your app
  
} else {
  console.log('Please install MetaMask!');
}
```

Now we have it we can use it.

The ```window.ethereum``` really boils down to having a request method that is used to make the RPC call via metamask. See the [jsonrpc](https://www.jsonrpc.org/specification) specification. 

The following commands are lower level JSON-RPC command that you don't need to use in the app as there are libraries that take away this complexity.

But this is good to know how this works.

Run these commands in the console.

```ethereum.request({ method: 'eth_requestAccounts' }).then(console.log)```

The API available on the request method is based on an interface exposed by all Ethereum clients, along with a growing number of methods that may or may not be supported by other wallets. 

See below.
- [Ethereum JSON-RPC Specification](https://eth.wiki/json-rpc/API#json-rpc-methods)
- [Ethereum JSON-RPC playground](https://playground.open-rpc.org/)
- [Metamask JSON-RPC API](https://docs.metamask.io/guide/rpc-api.html#rpc-api)
- [Ethereum-enabled DOM environments - eip-1102](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1102.md) 

The above method only asks the installed wallets for the account in use so lets talk to the node and get the latest block number.

```ethereum.request({ method: 'eth_blockNumber' }).then(console.log)```

This returns the latest block number in hexadecimal. So [convert it](https://www.rapidtables.com/convert/number/hex-to-decimal.html) and check the [latest block](https://goerli.etherscan.io/) on the network () In this case Goerli.

And to make an update transaction run this in the console. 0.01 ETH will be sent to the following contract address.

```
params = [{
  from: ethereum.selectedAddress, // must match user's active address.
  to: '0x3102Ad332fE01C98b1E9326dC5f5c57787c107DC', // Required except during contract publications. This is a contract address but could be any address.
  value: '0x2386F26FC10000', // 0.01 ETH or 10^16 wei // required to send ether to the recipient from the initiating external account.
}];

ethereum.request({method: 'eth_sendTransaction',params})
```

This above send ETH to the contract address but how do we call a function on the contract. 


To call the withdraw() function is a bit more work. 

You need to create some hex data of the function and the parameters and pass this to eth_sendTransaction. 
```
// Create a Hex representation of the function signiture.
web3.utils.sha3("withdraw(uint256)"); '0x2e1a7d4d13322e7b96f9a57413e1525c250fb7a9021cf91d1540d5b69f16a49f' 

The first 4 bytes of the hash are `2e1a7d4d` and is the function signiture value, which will tell the contract which function we want to call.

// Convert parameter value to wei and then hex
const withdraw_amount = web3.utils.toWei("0.01", "ether"); // '10000000000000000'
const withdraw_amount_hex = web3.utils.toHex(withdraw_amount); // '0x2386f26fc10000'

Now, we add the function selector to the amount (padded to 32 bytes), add this to the data property and remove the value property.

params = [{
  from: ethereum.selectedAddress, // must match user's active address.
  to: '0x3102Ad332fE01C98b1E9326dC5f5c57787c107DC', // Change to contract address.
  data: '2e1a7d4d000000000000000000000000000000000000000000000000002386f26fc10000'
}];

ethereum.request({method: 'eth_sendTransaction', params})
```

See below for full metamask parameter details.
- [Sending transactions](https://docs.metamask.io/guide/sending-transactions.html#sending-transactions)


### Web3.js

You will see we are using a library called web3.js.

The web3.js library is a collection of modules that contain functionality for the ethereum ecosystem.

It has a lot more convenience methods to query the client node and the contract. 

See the docs for install info - [web3.js docs](https://web3js.readthedocs.io/en/v1.7.1/getting-started.html#adding-web3-js)

We look at some major packages of web3.js.
- web3.eth
- web3.eth.Contract
- web3.utils

First you need to wrap provider with this library and gain access to this 

```const web3 = new Web3(provider)```

Now you can make transactions on web3 instance. If your in the console make sure to expose this to the window environment.

#### web3.eth
The web3.eth package allows you to interact with an Ethereum blockchain and Ethereum smart contracts.

Here are some convenience methods.

```
   const accounts = await web3.eth.getAccounts()

   web3.eth.getBalance(accounts[0]).then(console.log)

   web3.eth.getBlockNumber().then(console.log)
```

```
// send to contract address
params = {
  from: ethereum.selectedAddress,
  to: '0x3a86b0B67c275f1E69B6415f4a4653F3e0451ba6', // change contract address
  value: '10000000000000000', // in wei so 0.01 ETH 
};

web3.eth.sendTransaction(params, (e, txhash) => console.log("txhash", txhash))

// send to contract addFunds() function and the first 4 bytes of the hash is a26759cb.
Note that the parameter value is used as we are sending to a payable function in the contract.
params = {
  from: ethereum.selectedAddress, // must match user's active address.
  to: '0x3a86b0B67c275f1E69B6415f4a4653F3e0451ba6', // Change to contract address.
  value: '0x2386f26fc10000', // can send hex
  data: 'a26759cb0000000000000000000000000000000000000000000000000000000000000000' 
};

web3.eth.sendTransaction(params, (e, txhash) => console.log("txhash", txhash))
```

#### web3.eth.Contract
You can use sendTransaction to call functions on Contracts or you can create contract instances to use with web3.eth.Contract.

This is the preferred way, and you will need to pass the JSON ABI anf the contract address to web3.eth.Contract.

```
const { abi } = await fetch(`http://localhost:3000/contracts/Faucet.json`).then(res => res.json())

let contract = new web3.eth.Contract(abi, '0x3a86b0B67c275f1E69B6415f4a4653F3e0451ba6')
```
As mentioned the contracts when compiled are made public so are available in the console via a fetch().

You will see the contract instance has a methods object that has convenience methods to call.

```
// notice we don't need the contract address
params = {
  from: ethereum.selectedAddress,
  value: web3.utils.toWei('0.1', "ether")
};

contract.methods.addFunds().send(params);
```

You will notice in the Faucet app uses @truffle/contract. This is a further wrapper around web3.eth.Contract. Again just a higher level abstraction to communicate with a deployed contract..

Web3 and its dependencies are now bundled into @truffle/contract v4.0.2 or higher.

This is a more simplified interface but very similar functionality.

```
params = {
  from: ethereum.selectedAddress,
  value: web3.utils.toWei('0.1', "ether")
};

// @truffle/contract even more simpler calls
await truffleContract.addFunds(params);

or to send direct to the contract address

await truffleContract.send(web3.utils.toWei('0.1', "ether"), {
  from: ethereum.selectedAddress
})
```

#### web3.utils

Web3.js comes with many utils under [web3.utils](https://web3js.readthedocs.io/en/v1.7.1/web3-utils.html)  

Web3 uses BN.js as JavaScript can't deal with 256-bit integers. 

You can see this by running this command on a contract instance in the truffle console.

```
 const instance = await Faucet.deployed()
 
 instance.address 
 
 await web3.eth.getBalance(instance.address)
 
 await instance.getFundersBalance()
 // BN { negative: 0, words: [ 0, <1 empty item> ], length: 1, red: null }
```

Also, there are plenty of conversion method from and to wei as we have seen earlier

```
web3.utils.toWei("0.1", "ether");
```

If you are using react checkout the React box to kickstart your project. Also, other libraries of interest below.

- [eip-1193](https://eips.ethereum.org/EIPS/eip-1193)
- [React Box](https://trufflesuite.com/boxes/react/)
- [@truffle/contract](https://github.com/trufflesuite/truffle/tree/master/packages/contract)
- [Web3-react]( https://github.com/NoahZinsmeister/web3-react/)
- [web3.js](https://web3js.readthedocs.io/en/v1.7.0/)
- [web3modal.js](https://github.com/trufflesuite/truffle/tree/master/packages/contract)
- [web3uiki](https://github.com/web3ui/web3uikit)


Alternatives to web3.js
- [ethers.js](https://docs.ethers.io/v5/)





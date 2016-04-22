# MetaMask Compatibility Guide

While MetaMask exposes the standard web3 API, there are few things to keep in mind:


### Web3 - Ethereum Browser Environment Check

Web3.js is injected into the javascript context.
Look for this before using your fallback strategy (local node / hosted node + in-dapp id mgmt / fail).
You can use the injected web3 directly but best practices is to replace it with your own version of web3.js
that you have used during development.


```js
if (typeof web3 !== 'undefined') {
  // Web3 has been injected by the browser (Mist/MetaMask)
  web3 = new Web3(web3.currentProvider);
} else {
  // fallback - use your fallback strategy (local node / hosted node + in-dapp id mgmt / fail)
  web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
}
```
from the [ethereum wiki on "adding web3"](https://github.com/ethereum/wiki/wiki/JavaScript-API#adding-web3)


### Async - Think of MetaMask as a light client

The user does not have the full blockchain on their machine and so data lookups can be a little slow.
For this reason, we are unable to support most synchronous methods. The exception to this is:
* eth_accounts
* eth_coinbase

see [ethereum wiki on "using callbacks"](https://github.com/ethereum/wiki/wiki/JavaScript-API#using-callbacks)


### Network check

When a user is interacting with a dapp via MetaMask, they may be on the mainnet or testnet. As a best practice, your dapp should inspect the current network via the `net_version` json rpc call. Then the dapp can use the correct deployed contract addresses for the network, or show an error message.


### Account management and transaction signing is managed externally to the dapp

Many Dapps have a built-in identity management solution as a fallback.
When an Ethereum Browser environment has been detected,
the user interface should reflect that the accounts are being managed externally.


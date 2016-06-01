# MetaMask Compatibility Guide

While MetaMask exposes the standard web3 API, there are few things to keep in mind. Below are hard requirements for MetaMask support as well as some best practices to keep in mind.

## Requirements

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

## Best Practices

### Network check

When a user is interacting with a dapp via MetaMask, they may be on the mainnet or testnet. As a best practice, your dapp should inspect the current network via the `net_version` json rpc call. Then the dapp can use the correct deployed contract addresses for the network, or show a message which network is expected.

see [ethereum wiki on "getNetwork" ] (https://github.com/ethereum/wiki/wiki/JavaScript-API#web3versionnetwork)

### Account management and transaction signing is managed externally to the dapp

Many Dapps have a built-in identity management solution as a fallback.
When an Ethereum Browser environment has been detected,
the user interface should reflect that the accounts are being managed externally.

see also [ethereum wiki on "accounts"] (https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethaccounts)

### Account List Reflects User Preference

When a user selects an account in MetaMask, that silently becomes the `web3.eth.defaultAccount` in your JS context, and becomes the only member of the `web3.eth.accounts` array, although this may change in the future.

Since these variables reflect user intention, but do not (currently) have events representing their values changing, we somewhat reluctantly recommend using an interval to check for account changes.

For example, if your application only cares about the `defaultAccount` value, you might add some code like this somewhere in your application:
```javascript
var account = web3.eth.defaultAccount;
var accountInterval = setInterval(function() {
  if (web3.eth.defaultAccount !== account) {
    account = web3.eth.defaultAccount;
    updateInterface();
  }
}, 100);
```
If you think this is an antipattern, and should be replaced with an event/subscription model, we encourage you to voice that opinion, let us know, and we could get an improved API adopted as an [EIP](https://github.com/ethereum/EIPs).

# MetaMask Compatibility Guide

While MetaMask exposes the [standard Ethereum web3 API](https://github.com/ethereum/wiki/wiki/JavaScript-API), there are few things to keep in mind. Below are requirements for MetaMask support as well as some best practices to keep in mind.

## Requirements

### Http(s) - Web Server Required

Due to browser security restrictions, we can't communicate with dapps running on `file://`. Please use a local server for development.

### Web3 - Ethereum Browser Environment Check

Web3.js is injected into the javascript context.
Look for this before using your fallback strategy (local node / hosted node + in-dapp id mgmt / read-only / fail).
You can use the injected web3 directly but best practices is to replace it with your own version of web3.js
that you have used during development.

Note that the environmental web3 check is wrapped in a `window.addEventListener('load', ...)` handler. This avoids race conditions with web3 injection timing.

```js
window.addEventListener('load', function() {

  // Checking if Web3 has been injected by the browser (Mist/MetaMask)
  if (typeof web3 !== 'undefined') {
    // Use Mist/MetaMask's provider
    web3 = new Web3(web3.currentProvider);
  } else {
    console.log('No web3? You should consider trying MetaMask!')
    // fallback - use your fallback strategy (local node / hosted node + in-dapp id mgmt / fail)
    web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
  }

  // Now you can start your app & access web3 freely:
  startApp()

})
```
This code snippet is modified from the [ethereum wiki on "adding web3"](https://github.com/ethereum/wiki/wiki/JavaScript-API#adding-web3)

### We Handle User Authorization

Forget what you know about key management, your Dapp likely won't need to call `sendRawTransaction` anymore.

Any time you make a call that requires a private key to sign something (`sendTransaction`, `sign`), MetaMask will automatically prompt the user for permission, and then forward the signed request on to the blockchain (or return it to you, if it was a call to `sign`).

Just listen for a response, and when the blockchain RPC has received the transaction and broadcast it, you'll get a callback.

### All Async - Think of MetaMask as a light client

The user does not have the full blockchain on their machine and so data lookups can be a little slow.
For this reason, we are unable to support most synchronous methods. The exception to this is:
* `eth_accounts` (`web3.eth.accounts`)
* `eth_coinbase` (`web3.eth.coinbase`)

See [ethereum wiki on "using callbacks"](https://github.com/ethereum/wiki/wiki/JavaScript-API#using-callbacks)

Not only is this a technical limitation, it's also a user experience issue. When you use synchronous calls, you block the user's interface, and so it's a generally bad practice anyways. Think of this API restriction as a gift to your users.

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

When a user selects an account in MetaMask, that silently becomes the `web3.eth.accounts[0]` in your JS context, the only member of the `web3.eth.accounts` array.

The `web3.eth.defaultAccount` variable should be considered a dapp-provided variable for your own convenience, but should not be used as a data source of user intention.

### Responding to Selected Account Changes

Since these variables reflect user intention, but do not (currently) have events representing their values changing, we somewhat reluctantly recommend using an interval to check for account changes.

For example, if your application only cares about the `web3.eth.accounts[0]` value, you might add some code like this somewhere in your application:
```javascript
var account = web3.eth.accounts[0];
var accountInterval = setInterval(function() {
  if (web3.eth.accounts[0] !== account) {
    account = web3.eth.accounts[0];
    updateInterface();
  }
}, 100);
```
If you think this is an antipattern, and should be replaced with an event/subscription model, we encourage you to voice that opinion, let us know, and we could get an improved API adopted as an [EIP](https://github.com/ethereum/EIPs).


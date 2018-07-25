# MetaMask Compatibility Guide

While MetaMask exposes the [standard Ethereum provider API](https://github.com/ethereum/wiki/wiki/JavaScript-API), there are few things to keep in mind. Below are requirements for MetaMask support as well as some best practices to keep in mind.

## Requirements :nut_and_bolt:

### :globe_with_meridians: Http(s) - Web Server Required

Due to browser security restrictions, we can't communicate with dapps running on `file://`. Please use a local server for development.

### :partly_sunny: Ethereum Browser Environment Check

See [Detecting MetaMask](./detecting_metamask.md) for more information.

### :dancers: We Handle User Authorization

Forget what you know about key management, your Dapp likely won't need to call `sendRawTransaction` anymore.

Any time you make a call that requires a private key to sign something (`sendTransaction`, `sign`), MetaMask will automatically prompt the user for permission, and then forward the signed request on to the blockchain (or return it to you, if it was a call to `sign`).

Just listen for a response, and when the blockchain RPC has received the transaction and broadcast it, you'll get a callback.

### :dizzy: All Async - Think of MetaMask as a light client

The user does not have the full blockchain on their machine, so data lookups can be a little slow.
For this reason, we are unable to support most synchronous methods. The exceptions to this are:
* `eth_accounts` (`web3.eth.accounts`)
* `eth_coinbase` (`web3.eth.coinbase`)
* `eth_uninstallFilter` (`web3.eth.uninstallFilter`)
* `web3.eth.reset` (uninstalls all filters).
* `net_version` (`web3.version.network`).

Usually, to make a method call asynchronous, add a callback as the last argument to a synchronous method. See the [Ethereum wiki on "using callbacks"](https://github.com/ethereum/wiki/wiki/JavaScript-API#using-callbacks)

Using synchronous calls is both a technical limitation and a user experience issue. They block the user's interface. So using them is a bad practice, anyway. Think of this API restriction as a gift to your users.

## Best Practices :bowtie:

### :construction_worker: Network check

When a user interacts with a dapp via MetaMask, they may be on the mainnet or testnet. As a best practice, your dapp should inspect the current network via the `net_version` json rpc call. Then, the dapp can use the correct deployed contract addresses for the network, or show which network is expected in a message.

For example:
```javascript
web3.version.getNetwork((err, netId) => {
  switch (netId) {
    case "1":
      console.log('This is mainnet')
      break
    case "2":
      console.log('This is the deprecated Morden test network.')
      break
    case "3":
      console.log('This is the ropsten test network.')
      break
    case "4":
      console.log('This is the Rinkeby test network.')
      break
    case "42":
      console.log('This is the Kovan test network.')
      break
    default:
      console.log('This is an unknown network.')
  }
})
```

See the [ethereum wiki on "getNetwork" ](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3versionnetwork)

### :squirrel: Account management and transaction signing is managed externally to the dapp

Many Dapps have a built-in identity management solution as a fallback.
When an Ethereum Browser environment has been detected, the user interface should reflect that the accounts are being managed externally.

Also see the [Ethereum wiki on "accounts"](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethaccounts)

### :raising_hand: Account List Reflects User Preference

When a user selects an account in MetaMask, that account silently becomes the `web3.eth.accounts[0]` in your JS context, the only member of the `web3.eth.accounts` array.

For your convenience, consider the `web3.eth.defaultAccount` variable a dapp-provided variable. However, it should not be used as a data source of user intention.

### :ear: Listening for Selected Account Changes

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
If you think this is an antipattern, and should be replaced with an event/subscription model, we encourage you to voice that opinion. Let us know, and we could get an improved API adopted as an [EIP](https://github.com/ethereum/EIPs).

## Download MetaMask Badge

If you'd like to encourage your users to download MetaMask, feel free to use either of these images, linking to [metamask.io](https://metamask.io):

![download metamask button](./images/download-metamask.png)
![download metamask button dark](./images/download-metamask-dark.png)

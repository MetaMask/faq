# Developing for MetaMask

MetaMask enables your website to read from the blockchain and propose transactions to the current user. To enjoy these benefits, you have to first check if the user has an Ethereum-compatible browser like MetaMask.

## Detecting MetaMask

Metamask/Mist currently inject their interface into page as global `web3` object.

```javascript
window.addEventListener('load', function() {

  // Checking if Web3 has been injected by the browser (Mist/MetaMask)
  if (typeof web3 !== 'undefined') {

    // Use the browser's ethereum provider
    var provider = web3.currentProvider

  } else {
    console.log('No web3? You should consider trying MetaMask!')
  }

})
```

The `provider` object is a low-level object with just one supported method: `provider.sendAsync(options, callback)`.

The `options` object has several useful fields: `method` (the method name), `from` (the sender address), `params` (an array of parameters to send to that method).

These methods all correspond directly to the options in [the RPC provider spec](https://github.com/ethereum/wiki/wiki/JSON-RPC).

## Installing Web3.js

Because defult `web3` API is not very user friendly, most developers will import a convenience library like Web3.js or EthJS using the bundler of their choice.

[The Web3.js library](https://github.com/ethereum/web3.js/) has [a guide for importing with different bundlers](https://github.com/ethereum/wiki/wiki/JavaScript-API#adding-web3).

## Installing EthJS

[EthJS](https://github.com/ethjs/ethjs) can be installed via NPM with npm or yarn by running `npm install`.

## Using a Convenience Library

Once you've detected an Ethereum browser, and imported a convenience library, you can initialize that library using the detected `web3.currentProvider` object. For web3 with browserify, this might look like this:

```javascript
var Web3 = require('web3')
var localWeb3 = new Web3(web3.currentProvider)
```

Initializing an EthJS instance looks similar:
```javascript
const Eth = require('ethjs');
const eth = new Eth(web3.currentProvider);
```

## Deprecation of Global Web3.js

Originally (and maybe still), Ethereum browsers injected an instance of the Web3.js convenience library as a global `window.web3` object.  This created difficulties for many users, because this convenience library's API changed often and casually, and so it was not a stable API surface to build a web app on top of.

To avoid this confusion in the future, the Ethereum browser developers (Mist & MetaMask) agreed on a smaller API surface to inject instead. This object, the Ethereum Provider, has always actually been the data source for `web3`, but formalizing it as the global API instead of `web3` forces developers to choose the convenience library version they prefer, and allows their code to rely on that API without breaking.

[On May 23, 2017](https://github.com/ethereum/mist/releases/tag/v0.9.0), the Mist browser announced they would eventually be deprecating the global `web3` object. MetaMask voiced their support for this deprecation, but the replacement API was not declared in that post. It even suggested that eventually `web3.currentProvider` would be deprecated, but to ensure backwards compatibility, MetaMask is going to continue to inject the ethereum provider as `web3.currentProvider` for the forseeable future.

The safest move forward for developers at the moment is to use the `web3.currentProvider` object, and using it to initialize convenience libraries if desired.

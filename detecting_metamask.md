# Developing for MetaMask

MetaMask enables your website to read from the blockchain and propose transactions to the current user. To enjoy these benefits, you have to first check if the user has an Ethereum-compatible browser like MetaMask.

## What is an Ethereum provider?

A provider is a low-level object with one primary method: `sendAsync(options, callback)`. The `options` object has several useful fields: `method` (the method name), `from` (the sender address), `params` (an array of parameters to send to that method).

These methods all correspond directly to the options in [the RPC provider spec](https://github.com/ethereum/wiki/wiki/JSON-RPC).

## Detecting MetaMask

MetaMask and other privacy-focused browsers require dapps to request a provider before using it. Some older browsers may still inject a Web3 instance automatically, so it's best practice to handle both cases:

```javascript
// If web3 is not injected (modern browsers)...
if (typeof web3 === 'undefined') {
    // Listen for provider injection
    window.addEventListener('message', ({ data }) => {
        if (data && data.type && data.type === 'ETHEREUM_PROVIDER_SUCCESS') {
            // Use injected provider, start dapp...
            web3js = new Web3(ethereum);
        }
    });
    // Request provider
    window.postMessage({ type: 'ETHEREUM_PROVIDER_REQUEST' }, '*');
}
// If web3 is injected (legacy browsers)...
else {
    // Use injected provider, start dapp
    web3js = new Web3(web3.currentProvider);
}
```

After user approval, a provider object will be injected as a `window.ethereum` global variable. To see if the injected provider is from MetaMask, you can check `ethereum.isMetaMask`.

## Installing Web3.js

The Web3.js API is sometimes treated as synonymous with the Ethereum provider, but in fact is a convenience library for using the provider. There are now many alternatives to web3.js, so rather than injecting a large library that only some dapps will rely on, b By default only an Ethereum provider will be injected after user approval, not a Web3 instance. If dapps require web3.js, they should be loading the specific version they require instead of relying on a version injected by the browser. However, for convenience, a Web3 instance can still be injected by passing a web3 flag when requesting a provider:

```js
// Request provider
window.postMessage({ type: 'ETHEREUM_PROVIDER_REQUEST', web3: true }, '*');
```

We do not make guarantees about what version of web3 will be injected in response to this request, so this method should only be used for convenience in development and debugging. [The Web3.js library](https://github.com/ethereum/web3.js/) has [a guide for importing with different bundlers](https://github.com/ethereum/wiki/wiki/JavaScript-API#adding-web3).

## Installing EthJS

[EthJS](https://github.com/ethjs/ethjs) can be installed via NPM with npm or yarn by running `npm install`.

## Using a Convenience Library

Once you've requested an Ethereum provider and imported a convenience library, you can initialize that library using the injected `ethereum` provider object. For web3 with browserify, this might look like this:

```javascript
var Web3 = require('web3')
var localWeb3 = new Web3(ethereum)
```

Initializing an EthJS instance looks similar:
```javascript
const Eth = require('ethjs');
const eth = new Eth(ethereum);
```

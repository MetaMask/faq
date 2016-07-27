# MetaMask Users FAQ

### Information on Buying Ether Within MetaMask

Check out our [Coinbase guide](./COINBASE.md)

### Using a local node

To run MetaMask against a local node you will need to lookup the url for the extension and list it as a CORS domain.
You can find the extension ID in the extensions panel `chrome://extensions/`.
Start geth with the following command, using your unique correct extension ID:
`geth --rpc --rpccorsdomain="chrome-extension://pgfcgpgggeefgnajgbdojefgdddlgnpi"`.

### Sandboxing MetaMask

 If you are concerned about giving strong permissions to a Chrome extension, you can use a separate Chrome profile for Metamask

 ![image](https://cloud.githubusercontent.com/assets/1474978/16848422/a0ea403a-49aa-11e6-9e62-71c19870dd87.png)

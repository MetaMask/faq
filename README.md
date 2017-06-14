# MetaMask FAQ

Here, we've got a variety of guides for people interested in MetaMask. Go ahead and check out the one that interests you:

- [Advanced Usage for Users](./USERS.md)
- [MetaMask Compatibility Guide for Dapp Developers](./DEVELOPERS.md)
- [How to get Ether Quickly with Coinbase](./COINBASE.md)
- [How to check the MetaMask Logs](./LOGS.md)

# Top FAQ's
## Q: I just imported an account. The balance is wrong! Where's my ETH?
* **Cause**: When you install the MetaMask plugin, it is connected to a test network and can't get your account balances. 
* **Solution**: Connect MetaMask to the **Ethereum Main Net** so it can get your real account balances.
* **Instructions**: Click the test network at the top of the plugin window and select **Ethereum Main Net** from the list that appears.
  
  ![click the test network](https://github.com/MetaMask/faq/blob/master/images/click-the-test-network.png)

  This action logs you out of MetaMask. When you log back into MetaMask with your password, MetaMask shows the correct account balances.

## Q: I can't use the import feature for uploading a JSON file! The window keeps closing when I try to select a file!
* **Cause**: This is a known bug in Google Chrome that appears in Ubuntu and some Windows builds.
* **Solution**: A good workaround is to open the extension url, chrome-extension://nkbihfbeogaeaoehlefnkodbefgpgknn/popup.html, directly in a new tab and import through the tab.

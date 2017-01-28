# Getting MetaMask Logs

If you've run into an issue, and reported it on [our Github](https://github.com/MetaMask/metamask-plugin/issues) or [our Slack](http://slack.metamask.io/), but we haven't been able to figure it out, we might ask you to get us some logs.

There are four types of logs that you might need to check:
- Popup Logs
- Background Logs
- UI State Logs
- Crash Logs

## Popup Logs (Chrome)

To get logs from the popup:

1. Click the MetaMask fox in the top right of your browser.
2. Wait for the popup to open (or partially open if that's part of the bug).
3. Right-click in the newly opened popup, and select `Inspect Element`.
4. Wait for the new `Inspector` window to open.
5. Click `Console` at the top of the `Inspector` window.
6. Look for any strange logs, especially red errors!

## Background Logs (Chrome)

To get logs from MetaMask's background process in Chrome:

1. Right-click the MetaMask fox in the top right of your browser.
2. Select `Manage Extensions`.

  ![image](https://cloud.githubusercontent.com/assets/1474978/22399076/f54b8d24-e549-11e6-9bf4-48bf8767f20d.png)

3. Ensure "Developer Mode" is selected in the top right.

  ![image](https://cloud.githubusercontent.com/assets/1474978/22398918/52c8e388-e546-11e6-92d5-dcf6daed7718.png)

4. Scroll down to `MetaMask`, and click the "Inspect views: `background page`" link.
5. Wait for the new `Inspector` window to open.
6. Click `Console` at the top of the `Inspector` window.
7. Look for any strange logs, especially red errors!

## UI State Logs (Chrome)

If you've found a visual glitch in the rendering of MetaMask, we might ask you for a UI State log.  To get one of these:

1. Follow the steps above to open the `Popup Logs (Chrome)`.
2. At the bottom of the console, enter the text `logState()` and hit `Enter`.
3. The console will then spit out a big bunch of text, something that looks like [this](https://github.com/MetaMask/metamask-plugin/blob/master/development/states/account-detail.json).
4. Copy that entire text blob and send it to us, ideally in [a new Github issue](https://github.com/MetaMask/metamask-plugin/issues/new).

## Crash Logs (Chrome)

If your bug involves crashing the whole browser, then having a browser console won't be much good! (It will be crashed).

In these cases, you'll need to start your browser in a way that it writes its logs to the disk, so you can open that log file after the browser crashes.

[This process is described here](https://www.chromium.org/for-testers/enable-logging), and we hope to write more specific steps on this in the future. If you have to go through this process, please do record the steps, so we can have more detailed instructions here for different platforms.

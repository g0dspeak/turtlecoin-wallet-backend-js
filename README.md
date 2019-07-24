![image](https://user-images.githubusercontent.com/34389545/35821974-62e0e25c-0a70-11e8-87dd-2cfffeb6ed47.png)

#### Master Build Status
[![Build Status](https://travis-ci.org/turtlecoin/turtlecoin-wallet-backend-js.svg?branch=master)](https://travis-ci.org/turtlecoin/turtlecoin-wallet-backend-js)

#### NPM
[![NPM](https://nodei.co/npm/turtlecoin-wallet-backend.png?compact=true)](https://npmjs.org/package/turtlecoin-wallet-backend)

#### Github

https://github.com/turtlecoin/turtlecoin-wallet-backend-js

# turtlecoin-wallet-backend

Provides an interface to the TurtleCoin network, allowing wallet applications to be built.

* Downloads blocks from the network, either through a traditional daemon, or a blockchain cache for increased speed
* Processes blocks, decrypting transactions that belong to the user
* Sends and receives transactions

## Installation

NPM:

`npm install turtlecoin-wallet-backend --save`

Yarn:

`yarn add turtlecoin-wallet-backend`

## Documentation

[You can view the documentation here](https://turtlecoin.github.io/turtlecoin-wallet-backend-js/classes/_walletbackend_.walletbackend.html)

You can see a list of all the other classes on the right side of the screen.
Note that you will need to prefix them all with `WB.` to access them, if you are not using typescript style imports, assuming you imported with `const WB = require('turtlecoin-wallet-backend')`.

## Quick Start

You can find an [example project in the examples](https://github.com/turtlecoin/turtlecoin-wallet-backend-js/tree/master/examples/example1) folder.

### Javascript

```javascript
const WB = require('turtlecoin-wallet-backend');

(async () => {
    const daemon = new WB.Daemon('127.0.0.1', 11898);
    /* OR
    const daemon = new WB.Daemon('blockapi.turtlepay.io', 443);
    */
    
    const wallet = WB.WalletBackend.createWallet(daemon);

    console.log('Created wallet');

    await wallet.start();

    console.log('Started wallet');

    wallet.saveWalletToFile('mywallet.wallet', 'hunter2');

    /* Make sure to call stop to let the node process exit */
    wallet.stop();
})().catch(err => {
    console.log('Caught promise rejection: ' + err);
});
```

### Typescript

```typescript
import { WalletBackend, Daemon, IDaemon } from 'turtlecoin-wallet-backend';

(async () => {
    const daemon: IDaemon = new Daemon('127.0.0.1', 11898);

    /* OR
    const daemon: IDaemon = new Daemon('blockapi.turtlepay.io', 443);
    */

    const wallet: WalletBackend = WalletBackend.createWallet(daemon);

    console.log('Created wallet');

    await wallet.start();

    console.log('Started wallet');

    wallet.saveWalletToFile('mywallet.wallet', 'hunter2');

    /* Make sure to call stop to let the node process exit */
    wallet.stop();
})().catch(err => {
    console.log('Caught promise rejection: ' + err);
});
```

## Configuration

There are a few features which you may wish to configure that are worth mentioning.

### Auto Optimize

Auto optimization is enabled by default. This makes the wallet automatically send fusion transactions when needed to keep the wallet permanently optimized.

To enable/disable this feature, use the following code:

```javascript
wallet.enableAutoOptimization(false); // disables auto optimization
```

### Coinbase Transaction Scanning

By default, coinbase transactions are not scanned.
This is due to the majority of people not having solo mined any blocks.

If you wish to enable coinbase transaction scanning, run this line of code:

```javascript
wallet.scanCoinbaseTransactions(true)
```

### Logging

By default, the logger is disabled. You can enable it like so:

```javascript
wallet.setLogLevel(WB.LogLevel.DEBUG);
```

and in typescript:

```typescript
wallet.setLogLevel(LogLevel.DEBUG);
```

The logger uses console.log, i.e. it outputs to stdout.

If you want to change this, or want more control over what messages are logged,
you can provide a callback for the logger to call.

```javascript
wallet.setLoggerCallback((prettyMessage, message, level, categories) => {
    if (categories.includes(WB.LogCategory.SYNC)) {
        console.log(prettyMessage);
    }
});
```

and in typescript:

```typescript
wallet.setLoggerCallback((prettyMessage, message, level, categories) => {
    if (categories.includes(LogCategory.SYNC)) {
        console.log(prettyMessage);
    }
});
```

In this example, we only print messages that fall into the SYNC category.

You can view available categories and log levels in the documentation.

## Changelog

### v3.4.6

* Add `rewind()`

### v3.4.5

* Fix bug causing balance from sent transaction to appear in both locked + unlocked balance

### v3.4.4

* Fix bug with how forked transactions were handled
* Increase max sockets to use with request to fix timeouts in some environments
* Fix bug where transactions to yourself had an incorrect amount when locked

### v3.4.3

* Add `on('disconnect')` and `on('connect')` events for daemon
* Update `turtlecoin-utils` dependency

### v3.4.2

* Set keep-alive to true for `Daemon` and `BlockchainCacheApi`
* Use IP regex instead of `net` module to allow working in non node environments

### v3.4.1

* Fix transactions being broken

### v3.4.0

* Add type assertions for JavaScript users
* May possibly break your code if you were using implicit conversions to pass strings as numbers, etc
* Fix bug where `cancelledTransactionsFailCount` was not correctly initialized when loading from file
* Fix warning when using TLS with raw IP address
* Known regression - Transactions are broken. Update to 3.4.1

### v3.3.2

* Migrate from node-fetch to request for `BlockchainCacheApi` as it works better in odd environments
* Remove `maxBodyRequestSize` property as `abort-controller` significantly complicated code and didn't work in odd environments
* Known regression - Transactions are broken. Update to 3.4.1

### v3.3.1

* Improve auto optimization
* Update turtlecoin-utils dependency
* Known regression - Transactions are broken. Update to 3.4.1

### v3.3.0

* Adds `swapNode()` method

### v3.2.0

* Adds `getDaemonConnectionInfo()` method
* Removes compiled JavaScript from GitHub - GitHub install is no longer supported

### v3.1.1

* Fixes bug where wallet may not correctly halt after calling `stop()`.

### v3.1.0

* Adds `Daemon` class. This class supports Blockchain cache api's, conventional daemons, http and https, all automatically.
* Marks `ConventionalDaemon` and `BlockchainCacheApi` as deprecated. These will be removed in v4.0.0. Please use the `Daemon` class instead.

### v3.0.1

* Fix issue where `reset()` would cause double wallet scanning.

### v3.0.0

* Fix bug where using multiple wallet instances with different configs would only use the latest config.
* API change - You must now provide a config to the Utilities/ValidateParameters functions if you are using a non default config, for example if you are using the library for another cryptocurrency. Otherwise, the default TurtleCoin config will be used.

### v2.0.0

Start of changelog.

## Contributing

### Building (For Developers)

`git clone https://github.com/turtlecoin/turtlecoin-wallet-backend-js.git`

`cd turtlecoin-wallet-backend`

`npm install -g yarn` (Skip this if you already have yarn installed)

`yarn build`

Generated javascript files will be written to the dist/lib/ folder.

### Running tests

`yarn test` - This will run the basic tests

`yarn test-all` - This will run all tests, including performance tests.

### Before making a PR

* Ensure you are editing the TypeScript code, and not the JavaScript code (You should be in the `lib/` folder)
* Ensure you have built the JavaScript code from the TypeScript code: `yarn build`
* Ensure you have updated the documentation if necessary - Documentation is generated from inline comments, jsdoc style.
* Ensure you have rebuilt the documentation, if you have changed it: `yarn docs`
* Ensure the tests all still pass: `yarn test`, or `yarn test-all` if you have a local daemon running.
* Ensure your code adheres to the style requirements: `yarn style`

You can try running `yarn style --fix` to automatically fix issues.

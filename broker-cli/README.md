`kcli` - Kinesis Broker Command Line Interface
==============================================

`kcli` is the standalone command line interface for the Kinesis Broker Daemon, and is a simple client for its gRPC-based API.

Installation & Usage
--------------------

### Install Node.js and npm
  1. [Using the installer](https://nodejs.org/en/download/) (easiest)
  2. Using NVM
    1. Install nvm - `brew install nvm` or your favorite package manager
    2. Install the current LTS node and npm version - `nvm install --lts --latest-npm`

### Install `kcli`
Run `npm install -g https://github.com/kinesis-exchange/broker-cli.git`

This installs `kcli` globally, so you may need to give it additional permissions.

### Configure the client
You can set your default configuration by moving the [sample configuration](./sample-.kcli.js) to your home directory and renaming it `.kcli.js`.

You can do this on \*nix by running:
```
npm explore broker-cli -- cp -n ./sample-.kcli.js ~/.kcli.js
```

Or if you are already in the `broker-cli` directory,
```
cp -n ./sample-.kcli.js ~/.kcli.js
```

Once installed, in the correct location, you can edit the file to include your custom configuration.

Currently, supported custom configuration includes:
- RPC address of the Kinesis Broker Daemon you are controlling

You can view default configuration for `kcli` in [`./.kcli.default.js`](./.kcli.default.js).

### Run commands
Run `kcli --help` to see a list of available commands.

Things to try
-------------

### Streaming the orderbook
Run `kcli orderbook --market BTC/LTC` to get a live view of the orderbook.

I usually keep this running in a separate window while I do other commands.

### Checking your balance
Run `kcli wallet balance` to see the balance of your wallet in the supported currencies.

The “committed” balance is what you have available to trade.

### Placing a Limit Order
Run `kcli buy 0.01 100 --market BTC/LTC` to place a limit order to buy 0.01 BTC at a price of 100 LTC per BTC.

There is a corresponding `sell` command as well.

### Placing a Market Order
Run `kcli buy 0.01 --market BTC/LTC` to place a market order to buy 0.01 BTC.

Note that your order will not complete if there is not enough depth in the market to complete the order right away.

Notes
-----

### Orders and Block Orders
When interacting with the Broker Daemon as a user and submitting `buy` and `sell` orders, you are creating what we refer to as "Block Orders". These block orders can have different price restrictions (limit price, market price) and time restrictions (good-til-cancelled; fill-or-kill, immediate-or-cancel are forthcoming).

These block orders are then "worked" by the broker, meaning split up into individual actions on the Kinesis Network. Specifically, those actions are placing new limit orders and filling other brokers' limit orders.

This structure allows the end user to submit specific desires (e.g. market price, immediate-or-cancel) without relying on the Relayer to honor it -- instead the broker is responsible for interpreting and acting on user instructions.
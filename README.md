# Relaying Meta-Transactions Using Defender Client API

Demo code for relaying meta-transactions using [OpenZeppelin Defender](https://openzeppelin.com/defender) using the [client API](https://docs.openzeppelin.com/defender/relay-api-reference).

This project consists of a sample _names registry_ contract that accepts registrations for names either directly or via a meta-transaction, along with a client dapp, plus the meta-transaction relayer implementation.

This code is a revised version of [Workshop 01](https://github.com/OpenZeppelin/workshops/tree/master/01-defender-meta-txs) that makes use of `defender-client` on the mumbai network. Functionality is supported across any of Defender's [supported chains](https://docs.openzeppelin.com/defender/#networks) -- simply modify the code.

Live demo running at [defender-metatx-workshop-demo.openzeppelin.com](https://defender-metatx-workshop-demo.openzeppelin.com/).

[Video tutorial](https://youtu.be/Bhz5LJbq9YY)

[Written guide](https://docs.openzeppelin.com/defender/guide-metatx)

## Structure

- `app`: React code for the client dapp, bootstrapped with create-react-app.
- `autotasks/relay`: Javascript code for the meta-transaction relay, to be run as a Defender Autotask, compiled using rollup.
- `contracts`: Solidity code for the Registry contract, compiled with [hardhat](https://hardhat.org/).
- `scripts`: Custom scripts for common tasks, such as uploading Autotask code, signing sample meta-txs, etc.
- `src`: Shared code for signing meta-txs and interacting with the Forwarder contract.
- `test`: Tests for contracts and Autotask.

## Scripts

- `yarn deploy`: Compiles and deploys the Registry and Forwarder contracts to mumbai, and writes their addresses in `deploy.json`.
- `yarn sign`: Signs a meta-tx requesting the registration of `NAME`, using the relayer client credentials stored in `.env.relay`, and writes it to `tmp/request.json`.
- `yarn events`: Lists all the `Registered` events from the deployed contract on mumbai.
- `yarn invoke`: Invokes the relay Autotask via `WEBHOOK_URL` with the contents of `tmp/request.json` generated by `yarn sign`.
- `yarn create-autotask`: Compiles and creates the Autotask and uploads the Autotask code.
- `yarn upload`: Compiles and uploads the Autotask code to `AUTOTASK_ID`.
- `yarn relay`: Runs the relay Autotask script locally, using the Defender Relayer for `RELAY_API_KEY`.
- `yarn test`: Runs tests for contracts and Autotask using hardhat.

## Environment

Expected `.env` file in the project root:

- `PRIVATE_KEY`: Private key used for signing meta-txs locally.
- `TEAM_API_KEY`: Defender Team API key, used for uploading autotask code.
- `TEAM_API_SECRET`: Defender Team API secret.

Expected `.env` file in `/app`:

- `REACT_APP_WEBHOOK_URL`: Webhook of the Autotask to invoke for relaying meta-txs.
- `REACT_APP_QUICKNODE_URL`: Optional URL to Quicknode for connecting to the mumbai network from the dapp.

## Run the code

To run the workshop code yourself you will need to [sign up to Defender](https://defender.openzeppelin.com/).

### Fork and clone the repo

First fork the repository and then Git Clone your fork to your computer and install dependencies

```js
$ git clone https://github.com/sarvagnakadiya/gasless-meta-transactions.git
$ cd workshops/gasless-meta-transactions/
$ yarn
```

### Configure the project

Create a `.env` file in the project root

```js
PRIVATE_KEY="Private key used for signing meta-txs locally"
TEAM_API_KEY="Defender Team API key, used for uploading autotask code"
TEAM_API_SECRET="Defender Team API secret"
```

Store the value of a new private key in our projects `.env` file.

### Create Relayer

Create a relayer using [Defender Relay Client](https://docs.openzeppelin.com/defender/relay-api-reference) on mumbai.

```js
$ yarn create-relay
```

This runs a script that creates a relayer and stores the relayer API key and API secret in the projects `.env` file.

### Deploy contracts

Use the newly created Relayer to deploy the MinimalForwarder and Registry contracts to mumbai

```js
$ yarn deploy
```

### Sign Using Relayer

Sign a request to register a name, this will create a request in `tmp/request.json` that we can then view

```js
$ NAME=alice yarn sign
$ cat tmp/request.json
```

We can then use the script to send the request to our relayer, and [view the transaction on Etherscan](https://mumbai.etherscan.io/).  We can also view the name registrations.

```js
$ yarn relay
$ yarn events
```

### Create Autotask

Create an [Autotask using Defender Client](https://docs.openzeppelin.com/defender/autotasks-api-reference), with a webhook trigger and connected to our mumbai relayer.

```js
$ yarn create-autotask
```

This creates the autotask, saves the Autotask ID to the .env file [AUTO_TASK_ID]), and uploads the autotask code.

Grab the Autotask webhook from the web app and store in the apps `.env` file (in the `app` directory).

### Run app

We can then install dependencies using `yarn` and run the app.

```js
$ cd app
$ yarn
$ yarn start
```

1. Open app: [http://localhost:3000/](http://localhost:3000/)
2. Change to Mumbi network in Metamask
3. Enter a name to register and sign the metatransaction in MetaMask
4. Your name will be registered, showing the address that created the metatransaction and the name.

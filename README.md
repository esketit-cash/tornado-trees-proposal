# Tornado.cash trees governance proposal [![Build Status](https://github.com/esketit-cash/tornado-trees-proposal/workflows/build/badge.svg)](https://github.com/esketit-cash/tornado-trees-proposal/actions)

This repo deploys governance proposal for [TornadoTrees](https://github.com/esketit-cash/tornado-trees) update. It significantly reduces the cost of updating tornado merkle trees by offloading onchain updates to zkSNARKs.

## Audits

- [ABDK](audits/ABDK_proposal_audit.pdf)
- ZeroPool [1](./audits/ZeroPool_tornado_proxy_audit.pdf) [2](./audits/ZeroPool_tornado_trees_audit.pdf)

## Dependencies

1. node 12
2. yarn

## Init

```bash
$ yarn
$ cp .env.example .env
$ vi .env
```

## testing

```bash
$ docker build . -t tornadocash/tornado-trees-proposal && docker run -v `pwd`/proofsCache:/app/proofsCache tornadocash/tornado-trees-proposal
```

## mainnet instructions

### #before proposal creation

1. have you added new instances on the UI?
2. make sure you have set all the ens names for the instances

### #proposal creation

1. go to tornado-tress repo
1. `docker build . -t tornadocash/tornado-trees` you will need 50GB RAM
1. `docker run --rm -it --name tornadoTrees tornadocash/tornado-trees bash` just leave it and go to the next steps
1. `docker cp tornadoTrees:/app/artifacts/circuits/* backup`
1. send the backup folder to telegram

1. go to proposal repo
1. `docker cp tornadoTrees:/app/artifacts/circuits/BatchTreeUpdateVerifier.sol snarks`
1. edit `env`
1. `npx hardhat searchParams` and use the output in deployMainnet script
1. `yarn deployMainnet`
1. test the proposal on fork
1. verify on etherscan (add ASCII!!)
1. create proposal on forum to ask community to create the governance proposal
1. `goerli`: deposit and withdraw 500 deps using bot

### #during voting

1. make sure you have at least 10 ETH for the root-updater
1. run the old root-updater
1. update tornadoProxy and instances addresses on relayer

### #after the proposal execution

#### root updater

1. `git clone https://github.com/esketit-cash/tornado-root-updater.git -b migration && cd tornado-root-updater`
1. edit `.env`
1. run `generateCacheEvents` (uncomment last lines there 1 by 1)
1. `docker build . -t tornadocash/tornado-root-updater` should be run on the same server as for `tornado-trees`
1. run both

- `docker run --rm -e MIGRATION_TYPE=deposit tornadocash/tornado-root-updater`
- `docker run --rm -e MIGRATION_TYPE=withdrawal tornadocash/tornado-root-updater`

1. update ENS for proxy, trees
2. after finish, create the `deposits.json` and `withdrawals.json` cache using `allEvents` from `events.js` and move it to the `snark` branch
3. create the final release on github

#### UI (make it as PR)

1. change tornadoProxy and tornadoTrees addresses (run `node updateENS.js` in UI repo)
2. set notification on index page. "Mining is temporarily unavailable"
4. update account events cache
5. deploy UI
6. update cache events for mining
7. remove mining notification
8. redeploy UI

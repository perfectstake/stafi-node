# stafi-node

[Stafi](http://stafi.io) is:
- The First Decentralized Protocol Unlocking Liquidity of Staked Assets

The protocol of Stafi is created by Substrate and adopts Nominated Proof-of-Stake (NPoS), which complete Staking by setting up Staking Contracts in the upper layer to communicate with public chains. The Staking process is immune to Stafi’s contracts, for the latter act as the account book during Staking. Tokens staked through contracts will be written in the contracts and finally be locked-up on the original chain.

## Note

Now we are mainly testing the functions of block generation, transfer, staking, etc. And this is to prepare for the POA(Supports staking, not transfer). We will open the Staking Contracts code later when we are ready. 

## Running from Source

### Building

We have released the public testnet(v0.1.1). Welcome to particpate in us. Download the source:

```bash
git clone https://github.com/stafiprotocol/stafi-node.git
cd stafi-node
git checkout v0.1.1
```

Install system dependencies(recommend ubuntu or macos):

```bash
./scripts/init.sh
```

Build Stafi:

```bash
cargo build --release
```

### Running

#### Stafi Testnet

```bash
./target/release/stafi --chain=testnet
```

If you want to be a validator, you should run with the --pruning=archive option.

```bash
./target/release/stafi --chain=testnet --pruning=archive
```

Note: By default, Validator nodes are in archive mode. If you've already synced the chain not in archive mode, you must first remove the database with stafi purge-chain and then ensure that you run Stafi with the --pruning=archive option. The --pruning=archive flag is implied by the --validator and --sentry flags, so it is only required explicitly if you start your node without one of these two options. 

You can see your node on [telemetry] (set a custom name with `--name "my custom name"`).

[telemetry]: https://telemetry.polkadot.io/#list/Stafi%20Testnet%20Seiya

### Upgrade

Make sure you are on the right branch.

```bash
git pull
cargo build --release
```

### Clean

If you need to start from beginning. You should clean your db.

```bash
./target/release/stafi purge-chain --chain=testnet
```

## Faucet
You need to have some FIS tokens to continue. The faucet is coming.


## Running using Docker
Coming

## Become a validator

### Bond FIS tokens

It is highly recommended that you make your controller and stash accounts be two separate accounts. For this, you will create two accounts by [Stafi-apps](http://apps.stafi.io/#/accounts) and make sure each of them have at least enough funds to pay the fees for making transactions. Keep most of your funds in the stash account since it is meant to be the custodian of your staking funds.

Make sure not to bond all your FIS balance since you will be unable to pay transaction fees from your bonded balance.

It is now time to set up our validator. We will do the following:

- Bond the FIS tokens of the Stash account. These FIS tokens will be put at stake for the security of the network and can be slashed.
- Select the Controller. This is the account that will decide when to start or stop validating.

First, open [Stafi-apps](http://apps.stafi.io/), go to the Staking section. Click on "Account Actions", and then the "+ Stash" button.

- Stash account - Select your Stash account. In this example, we will bond 10 FIS tokens - make sure that your Stash account contains at least this much. You can, of course, stake more than this.
- Controller account - Select the Controller account created earlier. This account will also need a small amount of FIS tokens in order to start and stop validating.
- Value bonded - How much FIS tokens from the Stash account you want to bond/stake. Note that you do not need to bond all of the tokens in that account. Also note that you can always bond more tokens later. However, withdrawing any bonded amount requires the duration of the unbonding period. On Stafi testnet, the unbonding period is 7 hours. On Stafi mainnet, the planned unbonding period is 14 days.
- Payment destination - The account where the rewards from validating are sent.

Once everything is filled in properly, click Bond and sign the transaction with your Stash account.

After a few seconds, you should see an "ExtrinsicSuccess" message. You should now see a new card with all your accounts (note: you may need to refresh the screen). The bonded amount on the right corresponds to the funds bonded by the Stash account.

### Set Session Keys

Once your node is fully synced, stop the process by pressing Ctrl-C. At your terminal prompt, you will now start running the node in validator mode.

```bash
./target/release/stafi --chain=testnet --validator --name "name on telemetry"
```
You can give your validator any name that you like, but note that others will be able to see it, and it will be included in the list of all servers using the same telemetry server. Since numerous people are using telemetry, it is recommended that you choose something likely to be unique.

#### Generating the Session Keys

On your server, it is easier to run this command (while the node is running with the default HTTP RPC port configured):

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```

The output will have a hex-encoded "result" field. The result is the concatenation of the four public keys. Save this result for a later step.

#### Submitting the setKeys Transaction

You need to tell the chain your Session keys by signing and submitting an extrinsic. This is what associates your validator with your Controller account.

Go to Staking > Account Actions, and click "Set Session Key" on the bonding account you generated earlier. Enter the output from author_rotateKeys in the field and click "Set Session Key".

Submit this extrinsic and you are now ready to start validating.


### Validate

To verify that your node is live and synchronized, head to Telemetry and find your node. Note that this will show all nodes on the Stafi network, which is why it is important to select a unique name!

If everything looks good, go ahead and click on "Validate" in Stafi-apps.

- Payment preferences - You can specify the percentage of the rewards that will get paid to you. The remaining will be split among your nominators.

Click "Validate".

If you go to the "Staking" tab, you will see a list of active validators currently running on the network. At the top of the page, it shows the number of validator slots that are available as well as the number of nodes that have signaled their intention to be a validator. You can go to the "Waiting" tab to double check to see whether your node is listed there.

The validator set is refreshed every era. In the next era, if there is a slot available and your node is selected to join the validator set, your node will become an active validator. Until then, it will remain in the waiting queue. If your validator is not selected to become part of the validator set, it will remain in the waiting queue until it is. There is no need to re-start if you are not selected for the validator set in a particular era. However, it may be necessary to increase the number of FIS tokens staked or seek out nominators for your validator in order to join the validator set.

Congratulations! If you have followed all of these steps, and been selected to be a part of the validator set, you are now running a Stafi validator!


## Compile error

1. failed to run custom build command for `wabt-sys`
```bash
/home/stafi/.cargo/registry/src/github.com-1ecc6299db9ec823/wabt-sys-0.7.2/wabt/src/option-parser.cc:60:20: error: MAKE_PROJECT_VERSIONwas not declared in this scope
     printf("%s\n", CMAKE_PROJECT_VERSION);
                    ^~~~~~~~~~~~~~~~~~~~~
make[2]: *** [CMakeFiles/wabt.dir/src/option-parser.cc.o] Error 1
make[1]: *** [CMakeFiles/wabt.dir/all] Error 2
make: *** [all] Error 2
```

This may be a problem with your cmake version. You can try this to fix it.

```bash
wget https://github.com/Kitware/CMake/releases/download/v3.17.3/cmake-3.17.3-Linux-x86_64.tar.gz
tar -xzvf cmake-3.17.3-Linux-x86_64.tar.gz

sudo mv cmake-3.17.3-Linux-x86_64 /opt/cmake-3.17.3

sudo ln -sf /opt/cmake-3.17.3/bin/*  /usr/bin/

cmake --version
```

More download versions on [Cmake](https://cmake.org/download/)
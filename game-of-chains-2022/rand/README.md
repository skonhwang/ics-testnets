# RAND Chain Information
RAND provide a random number generated by CCV Validator Set.
You can get a random number with following API.
```
$ curl localhost:1317/cosmos/base/tendermint/v1beta1/random
{
  "random": "9061238780083776738"
}
```

Contents

* [Status](#status)
* [Chain Data](#chain-data)
* [Relayer](#relayer)

## Status

* Timeline
  * 2022-12-06: Spawn Time : `2022-12-06T13:00:00.000000000Z`
  * 2022-12-06: Proposal 56 voting period ends
  * 2022-12-03: Proposal 56 goes into voting period
  * 2022-12-03: Genesis file without CCV state is generated

RAND will launch as a consumer chain through a governance proposal in the `provider` chain. Read the [Consumer Chain Start Process](/docs/Consumer-Chain-Start-Process.md) page for more details about the workflow.

The following items will be included in the proposal:
* Genesis file hash
  * The SHA256 is used to verify against the genesis file that the proposer has made available for review.
  * This "fresh" genesis file cannot be used to run the chain: it must be updated with the CCV states after the spawn time is reached.
* Binary hash
* Spawn time
  * Even if the proposal passes, the CCV states will not be available from the provider chain until after the spawn time is reached.

## Chain Data

### Binary

The binary published in this repo is the `randd` binary built using the `dsrvlabs/rand` repo tag [v0.0.1](https://github.com/dsrvlabs/rand/releases/tag/v0.0.1). You can generate the binary following the [Get Started section](https://github.com/dsrvlabs/rand/tree/v0.0.1#get-started).

  * [Linux amd64 build](randd)
  * Version: `main-23b756acb9090a44e666542f8afba8f0d96ecc32`
  * SHA256: `e42617c52c0bb51539861db6f834d4e1795e7424102cb25137763fcdb6e30314`

### Genesis file

**The genesis file for Interchain Security consumer chains must include the CCV (Cross Chain Validation) state generated by the provider chain _after_ the spawn time is reached.**

Final genesis file **with CCV state**: `**pending spawn time**`
- SHA256: `pending spawn time`
- Validators must replace their `config/genesis.json` file with this one before running the binary.

The genesis file with was generated using the following settings:

* Chain ID: `rand-1`
* Denom: `urand`
* Signed blocks window: `"8640"`
* Genesis accounts were added to provide funds for a faucet and a relayer that will be run by the testnet coordinators.
* Genesis file **without CCV state**: [`rand-fresh-genesis.json`](rand-fresh-genesis.json), SHA256: `902fa8275721b2f25199c89b16a8e53d03e85c0e4e28c1281cd5e5e660cc1670`
  * **This is provided only for verification, this is not the genesis file validators should be running their nodes with.**

## Endpoints

* **p2p persistent peers : `88aef9532d8825c467c0a6a2f090d1f278cb0c03@164.92.169.128:46656,6dbf79b2f9657c4049715eb04c671c0c04407de3@46.101.195.113:46656`**
* These peers represent the `DSRV` validator. please consider sharing your peers in discord, or create a PR to peers.txt
* Please keep in mind that any validator that does not come online after 67% of the voting power is up and running, is likely to be slashed for downtime, potentially resulting in being jailed (the `signed_blocks_window` parameter is set to `8640`).

## Join via Bash Script

On the node machine:
- Copy the `node_key.json` and `priv_validator_key.json` files for your validator.
  - **These should be the same ones as the ones from your provider node**.
- Run one of the following scripts:
  - RAND service: [rand-init.sh](rand-init.sh)
  - Cosmovisor service: [rand-init-cv.sh](rand-init-cv.sh)
- Wait until the spawn time is reached and the genesis file with the CCV states is available.
- Overwrite the genesis file with the one that includes the CCV states.
  - The default location is `$HOME/.rand/config/genesis.json`.
- Enable and start the service:
  - RAND
    ```
    sudo systemctl enable rand
    sudo systemctl start rand
    ```
  - Cosmovisor
    ```
    sudo systemctl enable cv-rand
    sudo systemctl start cv-rand
    ```
- To follow the log, use:
  - RAND: `journalctl -fu rand`
  - Cosmovisor: `journalctl -fu cv-rand`
- If the log does not show up right away, run `systemctl restart systemd-journald`.

## Relayer
If you want to get 20 points for task 21 [Run a relayer](https://github.com/hyphacoop/ics-testnets/tree/main/game-of-chains-2022#run-a-relayer) in game-of-chain, you just follow below guide.

0. Clone this repo
```sh
$ git clone https://github.com/dsrvlabs/ics-testnets.git
```

1. Move to relayer folder
```sh
$ cd ics-testnets/game-of-chain/relayer
```

2. Edit `run_rly.sh`

Input your validator account mnemonic, memo, rpc and grpc address.

_If you don't have grpc, you can leave empty string. ex. `GRPC_XXXXXX=""`_

```sh
  3 providerMnmonic="24 words"
  4 memo="Your relayer information"
  5 chains=("provider" "sputnik" "hero" "neutron" "gopher")
  6 RPC_PROVIDER="https://127.0.0.1:26657"
  7 GRPC_PROVIDER="https://127.0.0.1:29090"
  8 RPC_SPUTNIK="https://127.0.0.1:26657"
  9 GRPC_SPUTNIK="https://127.0.0.1:29090"
 10 RPC_HERO="https://127.0.0.1:26657"
 11 GRPC_HERO="https://127.0.0.1:29090"
 12 RPC_NEUTRON="https://127.0.0.1:26657"
 13 GRPC_NEUTRON="https://127.0.0.1:29090"
 14 RPC_GOPHER="https://127.0.0.1:26657"
 15 GRPC_GOPHER="https://127.0.0.1:29090"
```

3. Execute `run_rly.sh`

```sh
$ bash run_rly.sh
```
You can find your relayer's valset update count with this tool([goc-relayer-evidence](https://github.com/gnongs/goc-relayer-evidence))I, Please check out this repo.

If you reached 500 times of relay, Please stop relayer for others :D.

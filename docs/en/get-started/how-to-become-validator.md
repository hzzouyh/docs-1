# How to run a validator

## 1. Install and deploy a full node

Please follow the [tutorial](./how-to-join-testnet.md) to deploy the full node of the testnet and ensure that it is synchronized to the latest block height.

## 2. Set the environment

``` bash
nchcli config chain-id nch-testnet
nchcli config output json
nchcli config indent true
nchcli config trust-node true
```

## 3.Create an account

```shell
# The content in the example <> needs to be replaced according to the situation.

nchcli keys add <key_name>
# Enter the password for the encrypted account as prompted (required for subsequent transactions), and carefully save the information returned by the command
```

## 4. Obtain test Token

To get the test token, please refer to [here](./testcoin.md)

## 5. Create a validator

```shell

nchcli tx staking create-validator \
  --amount=10000pnch \
  --pubkey=$(nchd tendermint show-validator -o text) \
  --moniker=<key_name> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="100" \
  --from=$(nchcli keys show -a <key_name>)
  
```

## 6. Query validators

```shell
nchcli query staking validators

# You can find the newly added validator lucy in the list

[
  {
    "operator_address": "nchvaloper18q4pv9qvmqx7dcd2jq3dl3d0755urk8300709e",
    "consensus_pubkey": "nchvalconspub1zcjduepqua3tt6kl7v7sd558m24fj3s039fhmsxcv9fc49rqn0uwcuelvrmsdp3hwt",
    "jailed": false,
    "status": 0,
    "tokens": "10000",
    "delegator_shares": "10000.000000000000000000",
    "description": {
      "moniker": "lucy",
      "identity": "",
      "website": "",
      "details": ""
    },
    "unbonding_height": "0",
    "unbonding_time": "1970-01-01T00:00:00Z",
    "commission": {
      "commission_rates": {
        "rate": "0.100000000000000000",
        "max_rate": "0.200000000000000000",
        "max_change_rate": "0.010000000000000000"
      },
      "update_time": "2019-10-30T11:21:01.013731989Z"
    },
    "min_self_delegation": "100",
    "self_delegation": "10000.000000000000000000"
  }
]

```

## 7. Let the validator just create block

Step5 creates a validator, and its status is 0 at this time, 0 means that it has not been bound because there is not enough pnch to mortgage;

1000000000000pnch is a voting power, and the minimum unit of voting power is 1. Only when it is> = 1 can it become the binding state 2 and become an active validator to produce blocks.

Therefore, at least 990000pnch needs to be mortgaged. You can use your own account to collateral yourself, or you can let other accounts collateral to your verifier. Here are shown separately:

Here you need to use the validator address corresponding to the lucy account in step 4. operator_address: nchvaloper18q4pv9qvmqx7dcd2jq3dl3d0755urk8300709e

### Delegate 990000pnch

```shell
nchcli tx staking delegate <address-validator-operator> 990000pnch --from=<key name>

e.g.:
nchcli tx staking delegate nchvaloper18q4pv9qvmqx7dcd2jq3dl3d0755urk8300709e 990000pnch --from=$(nchcli keys show -a <key name>)

```

## 8. Confirm the validator status

```shell
nchcli query staking validators

[
  {
    "operator_address": "nchvaloper18q4pv9qvmqx7dcd2jq3dl3d0755urk8300709e",
    "consensus_pubkey": "nchvalconspub1zcjduepqua3tt6kl7v7sd558m24fj3s039fhmsxcv9fc49rqn0uwcuelvrmsdp3hwt",
    "jailed": false,
    "status": 2,
    "tokens": "1000000",
    "delegator_shares": "1000000.000000000000000000",
    "description": {
      "moniker": "lucy",
      "identity": "",
      "website": "",
      "details": ""
    },
    "unbonding_height": "0",
    "unbonding_time": "1970-01-01T00:00:00Z",
    "commission": {
      "commission_rates": {
        "rate": "0.100000000000000000",
        "max_rate": "0.200000000000000000",
        "max_change_rate": "0.010000000000000000"
      },
      "update_time": "2019-10-30T11:21:01.013731989Z"
    },
    "min_self_delegation": "100",
    "self_delegation": "510000.000000000000000000"
  },
  {
    "operator_address": "nchvaloper133vmttt6n49jac5zn3z0klcpe7m8qluglfu58z",
    "consensus_pubkey": "nchvalconspub1zcjduepq3zr5cyenfyz8qprts7344nl8gclm3st669hyrhgy9gae7l8ajuus5uttte",
    "jailed": false,
    "status": 2,
    "tokens": "1000000",
    "delegator_shares": "1000000.000000000000000000",
    "description": {
      "moniker": "local-nch",
      "identity": "",
      "website": "",
      "details": ""
    },
    "unbonding_height": "0",
    "unbonding_time": "1970-01-01T00:00:00Z",
    "commission": {
      "commission_rates": {
        "rate": "0.100000000000000000",
        "max_rate": "0.200000000000000000",
        "max_change_rate": "0.100000000000000000"
      },
      "update_time": "2019-10-30T08:10:34.407927185Z"
    },
    "min_self_delegation": "1",
    "self_delegation": "1000000.000000000000000000"
  }
]

# You can see that the status of the newly added validator lucy becomes 2, an active validator, you can check the block status through the block browser
```

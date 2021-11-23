# Multi Sig

The idea of securing your team funds/having a decentralized consortium described through a contract is really exciting to me. As we continue to advance with crypto economics and science I feel we will all begin to form these groups where issues such as deciding the direction of a group or enacting a collaborative action can be done entirely on-chain, quickly and cheaply.

Getting there is a challenge, we started with just multi sig as an idea on Bitcoin and now we have contracts on many chains capable of doing it. We got a new problem, we don't wanna be glued to the CLI for all proposals and then legends such as @octalmage makes a dapp to simplify the process of proposals. We now see a new problem, making the proposals can be hard with all these protocols and different formats of messages.

Included below are some example 'custom' JSON proposals you can use to help draft a multi sig proposal.

> Remember you must encode these jsons as base 64 in order to use it for the msg field. Here is a site to do that https://codebeautify.org/json-to-base64-converter

## Instantiate a CW20 Token

```json
{
  "decimals": 6,
  "initial_balances": [
    {
      "address": "myaddr",
      "amount": "1000000000"
    }
  ],
  "name": "My New Token",
  "symbol": "TOKEN"
}
```

## Transfer a CW20 token in possession of the multisig contract

```json
{
  "transfer": {
    "amount": "1000000000000",
    "recipient": "<address>"
  }
}
```

## Transfer 100000 milly of token

`[{"wasm":{"execute":{"contract_addr": "<yournewtokenaddr>", "funds":[], "msg": "<msg>"}}}]`

Where msg is a base64 encoded dict like this :

```json
{ "transfer": { "amount": "100000000000", "recipient": "<yournewtokenaddr>" } }
```

## Burn a number of tokens

```json
[
  {
    "wasm": {
      "execute": {
        "contract_addr": "<yournewtokenaddr>",
        "msg": "<msg>",
        "funds": []
      }
    }
  }
]
```

Where msg is a base64 encoded dict like this :

```json
{
  "burn": {
    "amount": "100000000000000"
  }
}
```

### Terraswap interactions

Creating a liquidity pair

```json
{
  "create_pair": {
    "asset_infos": [
      {
        "token": {
          "contract_addr": "<yournewtokenaddr>"
        }
      },
      {
        "native_token": {
          "denom": "uusd"
        }
      }
    ]
  }
}
```

Encode the above with base 64 and provide as msg to this call

```json
[
  {
    "wasm": {
      "execute": { "contract_addr": "addr", "msg": "<msg>", "funds": [] }
    }
  }
]
```

Before providing liquidity you need to increase the allowance of the LP pair:

```json
  "increase_allowance": {
    "amount": "<amountyouintendtoputin>",
    "expires": {
      "never": {}
    },
    "spender": "<lppaircontractaddr>"
  }

```

You can now provide liquidity to the pool. Providing initial liquidity FROM the multisig
This provides 100000 tokens with 1000 UST setting the price at .10c

```json
{
  "provide_liquidity": {
    "assets": [
      {
        "info": {
          "token": {
            "contract_addr": "<yournewtokenaddr>"
          }
        },
        "amount": "100000000000"
      },
      {
        "info": {
          "native_token": {
            "denom": "uusd"
          }
        },
        "amount": "1000000000"
      }
    ]
  }
}
```

# Ethermint private network
Instructions on how to setup an ethermint private test network with tendermint consensus engine for development.


## Requirement
* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Ethermint](https://github.com/tendermint/ethermint)
* [Tendermint](https://github.com/tendermint/tendermint)


## Initialize local test network
Before you can run ethermint you need to initialize tendermint and ethermint with their respective genesis states:
> bin/init

This script will create the following accounts:

``` javascript
[{
  address: '0x7eff122b94897ea5b0e2a9abf47b86337fafebdc',
  ether: 200000,
  password: '1234'
},
{
  address: '0xc6713982649D9284ff56c32655a9ECcCDA78422A',
  ether: 50,
  password: '1234'
},
{
  address: '0x6719e3c6d1b277272c2e898acdffa493d16e0bf1',
  ether: 40,
  password: '1234'
},{
  address: '0xde1bdda8c41f379bd4c19059b1bd94a5d32935d1',
  ether: 30,
  password: '1234'
},{
  address: '0xdf55b36ab1c553140dcab9ffe3224621ff8db027',
  ether: 20,
  password: '1234'
},{
  address: '0xd988a5f1ab876f2500e6c5b8e26ba2fdcd108fda',
  ether: 10,
  password: '1234'
},
},{
  address: '0x2ff3d7c646cbfd82eb880834a1316076af0b635f',
  ether: 0,
  password: '1234'

},{
  address: '0xd1f9fd5bf8eb0cecd5339ae7a85897364517051e',
  ether: 70,
  password: '1234'
}
```


## Start tendermint
Before running ethermint you need to start tendermint node:
> bin/tendermint


## Start ethermint
After running tendermint, start ethermint in another shell with this command:
> bin/ethermint

## Start tendermint-lite
To run tendermint lite client run this command:
> bin/lite

## Attach geth
You can interact with the ethermint network by attaching geth to your node:
> bin/geth

This will present a javascript console where you can run various commands.

### Send tokens
Form the console attached to your ethermint node, you can send ether from one
 account to another.  First we check the balances of the from and to accounts:

```
> var from = web3.eth.accounts[1]
> personal.unlockAccount(from, '1234')
> web3.eth.getBalance(from)
20000000000000000000
> var to = web3.eth.accounts[2]
> web3.eth.getBalance(to)
10000000000000000000
```

Now we send ether:
```
> eth.sendTransaction({from: from, to: to, value: 1})
"0x9374959bdab5874d71a34531eccd7aa9755c2dd2293c148d3acc4ddfcbc29f8b"
```

If you check the ethermint node console, you will see this transaction logged:
```
Submitted transaction fullhash=0x9374959bdab5874d71a34531eccd7aa9755c2dd2293c148d3acc4ddfcbc29f8b recipient=0xd988a5f1ab876f2500e6c5b8e26ba2fdcd108fda
```

Now you can check the balances of the from and to account to verify that
ether was moved:

```
> web3.eth.getBalance(from)
19999621999999999999
```

NOTE: The reason more than 1 is gone from the ether balance is because it costs
ether to execute the send ether transaction.  This transaction fee is given to
the validator.

```
> web3.eth.getBalance(to)
10000000000000000001
```

### Deploy the greeter contract and call the greet method
From the attached console, you can deploy the
[greeter smart contract](https://www.ethereum.org/greeter) and
use it:
```
> loadScript('deploy.js');
```

If you check the ethermint node console, you will see this transaction logged:
```
Submitted contract creation fullhash=0x2b20a7955bdedcad94864bdfaffac672c80662cd3dbfbdbeabbcde8c3c8fd291 contract=0xb6b29ef90120bec597939e0eda6b8a9164f75deb
Submitted contract creation fullhash=0xb38954a16324fb573efa794947be4d0866f0937924c6d1b7962e1e88a069b4bc contract=0x70f94d58cc3fdcbeac7140f35a087da9fcd09b94

```

Now you can read the storage variable in deployed contract by this command. 
```
> greeter.greet();
"hello private test network"
```

NOTE: The contract in deploy.js is the default contract displayed in the
[online solidity compiler](https://ethereum.github.io/browser-solidity)

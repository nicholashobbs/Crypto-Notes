# Notes on Crypto

# Blockchain

a blockchain is a decentralized platform that runs smart contracts. a giant worldwide computer where each node has a copy of all the data. code runs in smart contracts which are immutable

what is a cryptocurrency

what is mining

blockchain is a public ledger of transactions, but with digital signatures

each transaction has an index

a digital signature is a function of a private key and a message

verifying any transaction requires knowledge of all previous transactions

the history of transaction is the currency

everybody keeps a copy

the block is a list of transactions + a number that makes 30 zeroes in a SHA256 hash

vault/wallet 

# Ethereum

a general purpose blockchain with VM running on it

ether is the currency of ethereum

one wei = 10^-18 ether

an address/account on ethereum is used to track a balance - it has a private key (password) and a public key (username)

in order to change a balance and hack the blockchain, you would need the computing power to change all previous transactions

gas is the cost of transactions on ethereum

smart contracts are programs which are accessible to everyone on the network

ethereum allows you to create your own token - ERC 20 describes the requirements for doing so

ERC 20 - fungible

ERC 721 - non-fungible

EVM is the ethereum virtual machine

Dapp - once a dapp is deployed it is permanently on the blockchain and can't be fixed

It consists of 3 parts - smart contracts, testing, and front end

transaction

receipt - contains transactionHash, blockHas, mined, from, to, events

gwei

location

hash

ethereum clients - GETH, Parity, Trinity

The original Ethereum is limited by TPS, block time, duplicate responsibility. Ethereum 2.0 is a proposed set of solutions which include:

layer 1 (in ethereum): sharding - breaks up network into groups of computers and proof of stake (casper) which reduces block time and helps scaling

layer 2 (on top of ethereum):plasma allows certain stuff to happen on a side chain, state channels are 2 way communication channels which do work on the side and then put the result back onto the block chain

Native Assets

Tokens

ethereum research forum 

solc - solidity compiler

web3 - library to communicate with clients

remix - a solidity IDE

truffle - a framework for dapps written in node.js

ganache - a local dev environment for blockchain

metamask - an ethereum wallet packaged as a browser extension

a dapp involves communication from wallet to frontend to smart contract 

# Smart Contracts

written in solidity, vyper, LLL

browser on your computer interacts with a website, which interacts with the blockchain, which interacts with smart contracts

eth blockchain includes EVM, storage, and consensus and runs on top of os and hardware

the building blocks of dapps

1. code contract
    1. pragma statement
    2. contract Name {}
    3. define state variables in contract
    4. define functions in contract `function add(uint id) public{}`
    5. create js test file in tests/test
        1. `const contract = artifacts.require('contract');`
        2. `contract('Name', () => {})`
        3. `it()`, computations assert
2. `truffle develop`
3. deploy contract `migrate --reset`
4. copy ABI/address 
5. install frontend dependencies `npm install` and `npm start`
6. instantiate web3
7. instantiate contract instance
8. call/transact with contract and get/modify data

# Solidity

a contract oriented, statically typed, compiled language

compiling produces and abi as well as bytecode

Wallet communicates with frontend on clientside. frontend communicates with server and an ethereum node. ethereum node communicates with smart contract on blockchain

Structure of file

inheritance

reentrancy attacks

hashing multiple values

generating random int

using libraries

a file can have multiple smart contracts

each instance of a smart contract has an address on the block chain

state variables and local variables "shadowing a variable"

```jsx
pragma solidity ^0.5.0; //pragma >=, <???

contract SimpleStorage {
    uint stateVar;

    function set(uint _localVar) public {
        stateVar = _localVar;
    }

    function get() public view returns (uint) {
        return storedData;
    }
}
```

## Variable Types

### Fixed Size

uint256

bool

uint

bytes32

address stores a blockchain address

address payable allows you to send ether with the smart contract to this address. Address payable can be cast to address, but not vice versa

### Variable Size

bytes

string

### Arrays

storage arrays - stay in the blockchain - array `uint[] public ids;`

arrayName.push(element);

arrayName[index];

delete arrayName[index]

memory arrays - just locally stored in the function they are used. declared using the memory keyword `type[] memory arrayName - new type[](#)` cannot have a dynamic size - must be instantiated with a number

arrays in functions `function fooBar(uint[] memory myArg internal returns(uint[] memory){}`

### Mappings

`mapping(keytype => valuetype) mappingName;`

`mappingName[key] = value;`

`delete mappingName[key];`

mappings have default values - attempting to access a nonexistent key returns a value

you can map to arrays or to mappings `mapping(address => mapping(address => bool))`  `mapping(address => uint[])`

access nested items with `mappingName[key][key]`

### Structs

`struct Uppercase { type variablename; ... }`

to create instances inside functions, use calldata??

`User memory user = User(arguments)`

or to make an array of structs

`Type[] arrayName;`

.prop set and update???

### Enums

`enum CAPS { OPTION, ...}`

instantiate with `ENUM name;`

### Memory Locations

storage - inside blockchain, persistent

memory - doesnt change on blockchain, just memory

stack - just available in function - not persistent or memory

calldata - only available in external/public where the data comes from a transaction with something external

### Visibility Keywords

private (default) - not really private because it is on the blockchain

public - can be read outside smart contract

internal

external

## Functions

### Function Modifier Keywords

view (previously constant) returns a value and can not modify

pure just does a computation

```jsx
function name() pure/view/public returns(type <memory/calldata>) {
}
```

### Function Visibility Keywords

public - can be called from inside and outside

external - only can be called from the outside

internal - can be called within smart contracts in the solidity file and those which inherit from it

private - can only be called within the smart contract

payable - function must be external to be payable. this means that you can send ether.

mapping

## Built-in Variables

transaction - tx.origin (original sender address)

messages - msg.value, msg.sender (most recent call)

blocks - block.timestamp,

.on(confirmation???)

## Constructors

`constructor(args) public initialize args`

admin pattern:

## Function Modifiers

`modifier myModifier()`

`function foo() external myModifier1 myModifier2 {}`

## Fallback Function

`function() external {}`

## Libraries

`library MyLibrary { structs, functions }`

`MyLibrary.function`

`using Library for Library.Struct`

a deployed library is on the blockchain

an embedded library is just in a smart contract

## Control Flow

```jsx
if( ! || && == ){ // no strict comparison === in solidity
} else if (condition) {
} else

for(uint i=0; i<10, i++){} // (init; stop; increment)

while(bool) {
	if(condition) {
		bool = false;
	}
}
break, continue
```

Constructor

ABI - application binary interface - the set of functions which can interact with the outside world. also reads to a known address

Address

msg.sender

msg.value

modifier

import

require

view/pure

returns 

memory

setdata

storage

internal

using

## Events

`Event EventName(type property,...)`

`function fName { emit Eventname(arguments, ... )`

events are just readable from the outside - they cost less gas than storage, so should be used for generating data rather than storing in smart contract

`contract.getPastEvents('EventName',{filter: {value: [this, or, this], and: this}})`

in the above, default filter is just the last element 

indexed keyword is required to use something as a filter

use a websocket to get realtime events

## Interaction Between Smart Contracts

to interact from a to b, interface of b and address of b are both required. you can use interface keyword or make another contract to define interface

`import OtherContract.sol`

## Child Contracts

contract facotry pattern

```jsx
function initNewChild()
	create pointer to addresS? 
contract Child
```

you have to create a function to call child if you want to use child functions with admin mechanism

## Token Transfer

owner operator addresses or smart contracts

import erc20

```jsx
function transfer() exernal {
	Token token = Token(Address)
	token.transfer(msg.sender, 100);

for owner???
token.approve
TransferToken transferToken = TransferToken
transferToken.transferFrom(owner, recipient, amount)
token.transfer(owner, amount)
```

## Error Handling

throw is deprecated in 0.5

`revert('explanation')`

`require(condition, 'explanation if fails')`

`assert(condition)`

.call is vulnerable to reentry attacks, should be avoided

## Assembly

more dangerous to write since it is lower level

opcodes - ethervm.io

`assembly { let a:= mload(0x40), mstore(a,1), sstore(address,payload), extcodesize()}`

## _;

2 kinds of assembly

# Remix

in remix, red functions are setters which modify data and therefore send a transaction - blue are those which just call a contract and get data

# Truffle

written in nodeJS

truffle boxes - example apps and project templates - use `truffle unbox ___`

truffle commands

```jsx
truffle develop // starts a local development blockchain
truffle migrate // compiles and deploys --reset to run in order migrations
truffle test // runs your tests

```

folder structure:

```jsx
contract/
	Migrations.sol //internal to truffle
migration/ //explains how to deploy - all .js files - uses contract artifacts which are injected by truffle
	//each migration exports a function
	
test/
	js tests
	sol tests
truffle-config.js
build/ //created after compile
public/ //compiled frontend (js bundle, html) after webpack
client/ //frontend code
```

structure of files within folder

contracts

artifacts

migration

truffle-config

# Ganache

ganache is a development environment for blockchain

ganache-cli options:

```jsx
ganache-cli -h (host) -p (port) -a (# addresses) -g (gas price) -l (gas limit)
-e (amount of ether in accounts) -b (block time) -t (when to start)-d (seed) -k (hard-fork)
-n (secure option) -u (unlock certain account or # addresses) --account (add account)? 
--account_keys_path (saves keys in file) --db (where to store blockchain database)
-v (log all json-rpc requests) -q (log fewer json-rpc requests) - ? (help)
```

in ganache, blocks are automined

it generates 10 unlocked addresses by default with 100 fake ether in each. 

# OpenZeppelin

# Testing Solidity (Mocha)

1. write tests
2. make sure they fail at first
3. write code to make them pass
4. make sure they pass

1. arrange - input fake data
2. act - call test with fake data
3. assert - result is as expected

```jsx
contract('Name', () => {
	it('Description of test', async () => {
		const expected = thisthing; //arrange
		const something = await contract Name.deployed(); //act
		assert(expected == something)//assert
	}
} 
```

What to test - in a web app start with units, in smart contract, start with integration

every function with public or external must be tested

Mocha - collects tests, runs all tests, reports

Chai - uses 3 styles - assert (equal, lengthof, typeof), expect, should

happy/unhappy paths

# Testing on public testnet

frontend(web3) uses wallet to sign transaction, and sends the signed transaction to the smart contract on infura

first ganache, then ropsten, finally mainnet

use faucets to get eth on ropsten

## Infura

infura gives you node url to run on ropsten or other testnets (kovan)

[ethplorer.io](http://ethplorer.io) allows you to explore data of an erc20 token - pick an account, copy the address

use `ganache-cli -f <host from infura> -u <account from ethplorer>`

import abi of coin, etherscan → coin → contract

const recipient, unlocked, dai, instantiate web3, instantiate dai, run(), node script.js

# Web3

`const Web3 = require('web3');`

`const web3 = new Web3(provider);`

provider object is what actually communicates with the blockchain - it can just be a url

an isomorphic (frontend/backend) library for UI to interact with smart contracts

ethereum api allows you to interact with smart contracts using eth_call and eth_transaction

to use either, you must specify:

1. address
2. function
3. arguments
4. ether
5. other parameters

transaction has to be mined on a block (15s average)

call is instant

it uses a json-rpc protocol - meaning the api has one endpoint. You have to specify the encoding for the data

you need access to an ethereum node to use Web3. Use ganache to create a local node, or infura to create node on ropsten/kovan/mainnet

once you have your node setup,

1. import web3
2. create connection to Ethereum node
3. configure smart contract
4. pass an interface (abi) and an address to create a contract instance
5. this allows you to use the call and transact apis

web3 creates a contract instance which interacts with a smart contract

`const contract = new web3.eth.Contract(abi, address)`

address is optional - it uniquely identifies a contract on the block chain

call a function with `contract.methods.functionName.call({pass object})`

wallet exposes provider, dapp uses provider

.eth.getAccounts()

.send() - transaction

.call() - read only

HDWalletProvider - @truffle-hdwallet-provider is used serverside - put url from infura in HDWalletProvider - you can use an array of private keys, or a mnemonic

MetaMask is used on the client. To integrate with web3:

frontend sends request to sign to metamask, user is asked to confirm. if yes, metamask sends signed to smart contract

use getWeb3 to easily handle several cases - modern metamask, old metamask, and others

### Web3 Utility Functions

after new Web3

`.toWei()` and `.fromWei()` works with strings or `num.toString()` - optionally specify a unity as argument like ether or gwei

`.toBN()` converts anything to a big number

`.hexToAscii(hexString)` 

`.asciiToHex()` 

`.isHex()` 

`.toHex()` 

`.padLeft()` 

`.randomHex()` 

`.isAddress()` 

`.toChecksumAddress()` 

`.checkAddressChecksum()`

`.Sha3(value)`

`.SoliditySha3(param1, param2)`

## Debugging

syntax errors - solidity compiler helps

runtime error - EVM thinks you are doing something wrong - types include out of gas, stack overflow, stack underflow, invalid jump, static state change, invalid opcode, revert

logic errors - your conception is wrong

formal verification is the future of logic error debugging

use solidity linters/debugger/tests for runtime errors

for calls, use return to debug. for transactions, use events to debug

to use .transfer, cast address payable to address

analysis tab in remix has suggestions

# DeFi

Oracle  ****- allows you to inject outside data into the blockchain

provable - a blockchain oracle for dapps

DAO - decentralized autonomous organization. functions allow token owners to vote on changes to the smart contract

dydx 

Compound - allows you to borrow and lend ethereum and creates a liquidity pool

borrow ctokens, pay back plus interest

liquidity pool - sends a liquidity token, a liquidity provider sends ether and erco 20 tokens back

gnosis - bet on events in the real world with conditional tokens

Maker

UMA 

Nexus Material 

DEX ****- decentralized exchange

EOS ****- ****free transactions and faster/scalable, but more centralized

fungible

SAI - single collateral (v1 of dai)

DAI - a stablecoin secured by collateral - multiple types of collateral - any approved erc 20 token

uniswap - liquidity provider for traders. allows you to easily buy or sell any erc 20 tokens

uniswapfactoryinterface

createexchange

cUSDC - usd pegged token

burn - destroy tokens and get collateral back

mint - make token by committing collateral

# Opyn

Convexity Protocol

1. Create oTokens
2. Keep vaults collateralized
3. liquidate undercollateralized vaults
4. exercise tokens during expiry window

oToken

Reverse Dutch Auction

# Options

put

call

price

premium

option series

expiry

physical/cash settlement

collateral ratio

NPV

delta, theta, gamma, vega, rho

long/short

American/European/Bermudan

underlying

# Javascript

## Dom Manipulation

querySelector

querySelectorAll

getElementById

getElementsByClassName

getElementsByTagName

forEach

innerText

getAttribute

setAttribute

classList toggle add remove textContent includes

$domRefererence = getElementById('id');

.addEventListener

stack stores pointers and primitive types

heap stores reference types

preventDefault

Promises

async/await

arrow functions

imports

# React

# Svelte

structure

App.svelte

export - set from outside

UI/Data Binding

2 way data binding

Reactive value

Loops - Unique Key

Inline event handlers

Components

Global/Component CSS

Props (implied prop name = var name)

Event Forwarding/ Modifiers

Slots

Custom Events

# Webpack

monitors what is in the client folder - compiles modern js into vanilla js that can be used in many browsers. Also concats everything into a single file, resulting in a bundle - also creates a server

webpack.config.js - specifies the entry and output, in addition to a dev server

# Vim

hjkl

:sp

:vsp

dd

yy

ctrl + z

fg

operators - cdygn~!><=

text objects - aw, iw, ap, ab

%

$

+

fwbe

HML

zt, zz, zb

:e edit

:s/something/else/g (and options)

:buffers, :bp, :bn

:hls!

*,#

ctrl + ], ctrl + t

ctrl w+ s (horizontal) v (vertical)

open terminal buffer

ctags

tabs are window containers

windows are buffer viewpoints

buffers for file proxies

running bash from vim 

# Tmux

ctrl+b

% horizontal split

" vertical split

d - delete pane

:resize pane -UDLR #rows/columns 

# random notes

state of the dapps

dapp provider

DAU - daily active users

DEX, bancor, token store

unpkg allows you to use any node package without installing

how to tell if another address is a contract or regular address

ABIEncoderV2Pragma - enable experimental features
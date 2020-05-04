# Notes on Crypto

# Bitcoin and Blockchain

A blockchain is a decentralized platform that runs smart contracts, essentially a giant worldwide computer where each node has a copy of all the data. It is a globally shared, transactional database. The network timestamps transactions and hashes them into a chain of hash-based proof of work. Transactions are bundled into groups called blocks which are processed by miners The longest chain is proof that the majority of the pool of computing power has agreed on of the sequence of transactions that the network has witnessed.

In order to change something on the blockchain, you must send a transaction which is cryptographically signed and which is agreed upon by the network. If two transactions contradict, whichever is first is accepted

An electronic coin is a chain of digital signatures - each owner transfers the coin to the next by digitally signing a hash of the previous transaction and the public key of the next owner and adding this to the end of the coin. 

The core of Bitcoin is a timestamp server, which takes a hash of a block of transactions to be timestamped, a hash of the previous block, and attempts to find a nonce which in combination with this data gives the block's hash the required number of zero bits. 

The key of this protocol is that nothing in the block can then be changed without redoing the work of finding this nonce.

Mining refers to the process of incrementing and testing nonces in order to mine the block. The first transaction of the next block is a special transaction which generates a set amount of bitcoin and gives it to the miner.

The steps to run the network:

1. New transactions are broadcast
2. Each node collects transactions into a block
3. They work on finding a proof of work for this block
4. Once the nonce is found, it is broadcast to all nodes
5. Nodes accept the block only if the transactions are valid (not spent)
6. Acceptance of the block is indicated by working on creating the next block

If two nodes broadcast different versions, the other nodes in the network will save both branches until the next proof of work is found and one branch becomes longer.

Transactions are hashed into a merkle tree to save space.

# Ethereum

Ethereum is a general purpose blockchain with VM (the EVM) running on it, with a native currency called Ether.

It addresses several shortcomings of Bitcoin identified in the Ethereum whitepaper:

Lack of Turing completeness. Bitcoin doesn't support all computation - it is missing loops for example

Value-Blindness UTXO script cannot provide fine-grained control over amount that can be withdrawn

Lack of State - UTXO can only be spent or unspent, it doesn't keep any internal state besides this.

Blockchain-blindness - UTXO are not aware of nonce, previous hash, and other data about the blockchain

Ethereum intends to build a generalized framework which can imprrove upon scripting, altcoins and onchain protocols, and allow developers to create arbitrary applications

The state of ethereum is made up of objects called accounts - each has a 20 byte address. State transitions are transfers of value and information between accounts. Each account has four fields:

A nonce (counter to ensure transactions happen only once), an ether balance, a contract code, and storage (empty by default)

Ether is used to pay transaction keys. 

There are two types of accounts - externally owned (controlled by private keys) and contract accounts, controlled by code. Externally owned accounts have no code, and messages can be sent from them by creating and signing a transaction. 

For externally owned accounts, the address is the public key.

For smart contracts, the address is determined at the time of creation

Messages in Ethereum are similar to transactions in Bitcoin, with three differences. 

1. Messages can be created by external entity or a contract
2. Messages can contain data
3. The recipient, if it is a contract, has the option to return a response

Transaction in Ethereum refers to a signed data package that stores a message to be sent from an externally owned account. They can contain the recipient of the message, a signature, an amount of ether, as well as STARTGAS (a limit to the amount of gas a transaction can use) and GASPRICE (the amount to pay the miner per step). If a contract runs out of gas, all changes revert, except for the payment of fees. If a transaction stops, any remaining gas is returned to the sender.  

Every transaction has a message call which can create more message calls

Contracts have equivalent powers to external accounts. 

Ethereum state transition function APPLY(S,TX) → S':

1. Check if the transaction is well formed, signature is valid, and nonce matches what is in sender's account
2. Calculate transaction fee, subtract from sender's account balance, increment sender's nonce
3. Initialize GAS, take off an amount per byte to pay for the transaction
4. Transfer transaction value from sender's account to receiving account. If receiving account doesn't exist, create it. If it is a contract, run contract's code. If it runs out of gas, stop
5. If value transfer failed because of insufficient money or gas, revert all state changes, and add fees to miner's account
6. Otherwise, refund fees for all remaining gas, and send fees paid to the miner

EVM code consists of a series of bytes, each representing an operation.

Operations have access to three types of space

- The stack, a last-in first-out container with 32 byte values. This is where all computations are performed.
- Memory - infinitely expandable byte array. Freshly cleared for every message call
- Long term storage - key value store of 32 byte values. This is persistent, whereas the other two reset after computation ends.

Code can access value, sender, data of incoming message, as well as block header data, and return a byte array as output. 

Every account has storage.

# Solidity

## Basics

Solidity is a contract oriented, statically typed, compiled language.

A contract in solidity is roughly equivalent to a class in a regular Object Oriented language. Contracts have state variables (stored permanently), functions (which have internal/external, specific visibility, accept parameters, and return variables) modifiers (which do not support overloading), events (interfaces with EVM logging), structs (custom data structures which group variables), enums (which identify a finite set of constant options, and inheritance. In solidity, undefined/null does not exist, but variables always have a default value, which is different for each type. You can handle unexpected values with revert statements.

Contracts are structured as follows:

1. Pragma Statement (version)
2. Imports
3. Contract Declarations
    1. State Variables
    2. Functions
        1. define internal/external, visibility, returns, ???, arguments (and memory), modifiers
        2. local variables
        3. special functions:
            1. constructor() public
            2. receive()
            3. fallback()
            4. selfdestruct(receiver)
4. Comments - // and /**/ for regular, /// and /** **/ for natspec comments (Doxygen tags)

### Example Contract With Keywords

```jsx
pragma solidity ^0.5.0; //comment
/*
Multiline
Comments
*/
import `./AnotherContract.sol'; // avoid using ../

contract SimpleStorage {
    uint stateVar;
		string public otherVar; // has automatically created getter function

		constructor() public {
			// initialize stuff
		}
		/**
		@title natspec comments
    @notice return automatically created documentation
		@param _localVar is a local variable
		**/
    function set(uint _localVar) public {
        stateVar = _localVar;
    }

    function get() public view returns (uint) {
        return storedData;
    }
}
```

Compiling a smart contract produces an ABI (application binary interface) as well as bytecode (executable on EVM)

Time in Solidity - now returns a unix time. Default unit of time is seconds. You can also use the keywords minutes, hours, days, weeks

Natspec comments -

```jsx
@dev, @return, @title, @notice, @author, @file, @param, @version
```

## Variables

There are two scopes of variables in solidity: state variables, accessible within the entire smart contract, and local variables, which are only accessible in the function in which they are declared.

State variables are stored on the blockchain when a contract is deployed.

State variables which are declared as public are automatically created by the solidity compiler with a getter function which has the same name as the variable. For public arrays and mappings, the compiler creates a function which you pass an argument to in order to access a specific element

This keyword in solidity refers to the current contract

Variables can be made constant to save gas (means they can't be changed at all)

Immutable means they can't change once the contract is compiled.

### Value Types

Value types are always copied when used in an assignment. 

bool - can be true or false. Operators: `!, &&, || , ==, !=`

int/uint(size) - can be signed or unsigned, and size can be specified as any number of bits between 8-256. If size is not specified, they will be 256 bits. Bitwise Operators: `<, >, =, &, |, ^(xor), ~, >>, <<` General Operators: `+,-,*,/,&,**`

bytes32 - a set size bit array - has similar methods as uint - you can access items by index like any array.

fixed point exists in solidity, but is not fully supported.

address - stores an eth address (20 bytes). Has the methods .balance, .transfer, .send, .call, .callcode, .delegatetransfer

transfer costs 2300 wei

address payable - allows you to send ether with the smart contract to this address. It has the additional methods send and transfer. Address payable can be cast to address, but not vice versa. msg.sender is an address payable. 

contract - every contract is a variable of type contract. it can be implicity converted to what it inherits from, and explicity converted to/from an address

type(c) gives you information about a contract - including its .name, its .creationCode, and its .runtimeCode

### Reference Types (variable size)

Reference type (also referred to as complex types in solidity docs) have values which can be modified through multiple names - they are not copied whenever they are used. They must be given an explicit memory location when instantiated. 

bytes - a dynamically sized byte array

string - utf8, also dynamically sized

### Arrays

storage arrays - stay in the blockchain - array `uint[] public ids;`

arrayName.length

arrayName.pop(index);

arrayName.push(element);

arrayName[index];

delete arrayName[index]

Arrays can be declared with a specific size `uint[n] arrName;` creates an array of n items.

You can make multidimensional arrays  `uint[][] arrName;`

slicing `arrName[start:end];`

memory arrays - just locally stored in the function they are used. declared using the memory keyword `type[] memory arrayName = new type[](#)` cannot have a dynamic size - must be instantiated with a number

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

storage - inside blockchain, persistent - this is where state variables go, and is limited to the lifetime of the contract

memory - doesnt change on blockchain, just memory - this is limited to an external function call

stack - just available in function - not persistent or memory

calldata - only available in external/public functions where the data comes from a transaction with something external. Calldata is a special location that contains function arguments - only available, and also required for the parameters of external contract functions

## Functions

In general, functions have a name, modifiers, a defined visibility, and may or may not have a returns definition.

Arguments and returns must have a specified type.

Return variables must have a memory location

### Function Visibility Keywords

public - can be called from inside and outside

external - only can be called from the outside

internal - can be called within smart contracts in the solidity file and those which inherit from it

private (default) - can only be called within the smart contract. It is not really private because it is on the blockchain

### Function Modifier Keywords

view (previously constant) returns a value and can not modify the state

pure just does a computation, it can not read from or modify the state

payable - function must be external to be payable. this means that you can send ether.

```jsx
function name() pure/view visibility returns(type <memory/calldata>) {
}
```

In returns, you must either explicitly assign the variables which are returned in the returns statement itself, or specify return in the body of the function, as well as the type in the returns.

If the function returns a complex type, there must be a specified memory location for the return. 

## Built-in Variables

transaction - tx.origin (original sender address)

messages - msg.value, msg.data, msg.sender is the ETH address which is calling the function (the most recent call)

blocks - block.timestamp,

## Special Functions

The constructor runs once at the time a contract is instantiated. The receive function can be defined once, and runs when the contract receives a transaction. A contract with no receive goes to the fallback function when it receives ether. If neither exists, the contract cannot receive ether.

### Constructor

`constructor(args) public {};`

If the constructor has arguments, all derived contracts must specify each of these arguments.

If the constructor is declared as internal, the contract is 'abstract'

### Receive

`receive() external payable {};`

### Fallback

`fallback() external payable {};`

The fallback function must be external, it cannot return, and it cannot have arguments.

## Function Modifiers

`modifier myModifier()`

`function foo() external myModifier1 myModifier2 {}`

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
try {
} catch{
}
do {
} while(something){
}
return
```

ABI - application binary interface - the set of functions which can interact with the outside world. also reads to a known address

An Interface is an abstract contract with no functions, no constructor, no state variables

## Events

`Event EventName(type property,...)`

`function fName { emit Eventname(arguments, ... )`

events are just readable from the outside - they cost less gas than storage, so should be used for generating data rather than storing in smart contract

`contract.getPastEvents('EventName',{filter: {value: [this, or, this], and: this}})`

in the above, default filter is just the last element 

indexed keyword is required to use something as a filter

use a websocket to get realtime events

Anonymous events are cheaper to deploy and to call.

## Interaction Between Smart Contracts

to interact from a to b, interface of b and address of b are both required. you can use interface keyword or make another contract to define interface

`import OtherContract.sol`

create a contract in another contract using `D new D = new D(args);`

It is possible to create salted contracts???

## Child Contracts

contract factory pattern

```jsx
function initNewChild()
	create pointer to addresS? 
contract Child is Ancestor
```

you have to create a function to call child if you want to use child functions with admin mechanism

Multiple inheritance `contract Child is Ancestor, OtherAncestor`

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

2 kinds of assembly are functional and instructional

# Remix

in remix, red functions are setters which modify data and therefore send a transaction - blue are those which just call a contract and get data

transactions are shown in the console at the bottom - click to expand

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

has templates for tokens and more. IERC is an interface to an erc token

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

Gnosis - bet on events in the real world with conditional tokens. You can create sub-conditional tokens which combine events for more complex outcomes. 

erc 1155 - combination of erc20 and erc 721 tokens

dutchx

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

oToken primitives allow users to hedge risks, create leverage, buy insurance, bet on volatility. 

Framework for generalized put options - minting oTokens is analogous to minting DAI or yTokens. 

Convexity allows anyone to mint options - as long as they have enough collateral for the number of options she wants to mint. 

If options are of the same series, they are fungible with each other.

Convexity Protocol

1. Create oTokens
2. Keep vaults collateralized
3. liquidate undercollateralized vaults
4. exercise tokens during expiry window

## OToken - Create/Manage Options in Existing Markets

createETH/ERC20CollateralOption(amount, receiver)

addETH/ERC20CollateralOption(amount, receiver)

createAndSellETH/ERC20CollateralOption(amount, receiver)

addAndSellETH/ERC20CollateralOption(amount, vault, receiver)

openVault() - creates an empty vault

addETH/ERC20Collateral(vaultOwner)

issueOTokens(oTokensToIssue, receiver)

removeCollateral(amtToRemove)

burnOTokens(amtToBurn)

liquidate(vaultOwner, oTokensToLiquidate)

exercise(oTokensToExercise, vaultsToExerciseFrom)

redeemVaultBalance()

removeUnderlying()

strikePrice()

oTokenExchangeRate()

expiry()

underlyingRequiredToExercise(oTokensToExercise)

hasExpired()

isExerciseWindow()

hasVault()

getVaultByIndex(vaultIndex)

isUnsafe()

numVaults()

maxOTokensIssuable(collateralAmt)

## OptionsExchange (Buy/Sell)

buyOTokens(receiver, oTokenAddress, paymentTokenAddress, oTokensToBuy)

sellOTokens(receiver, oTokenAddress, paymentTokenAddress, oTokensToBuy)

premiumToPay(receiver, oTokenAddress, paymentTokenAddress, oTokensToBuy)

premiumReceived(receiver, oTokenAddress, paymentTokenAddress, oTokensToBuy)

## OptionsFactory (New Markets)

createOptionsContract(collateralType, underlyingType, strikePrice, strikeAsset, payoutType, expiry)

getNumberOfOptionsContracts

supportsAsset(asset)

# Unorganized

byzantine fault tolerant

sybil attacks

utxo

RANDOM STUFF
pragma experiental abiencoder v2

READING FROM THE STATE??

gives state variables, .balance, info about block, tx, msg, any non-pure functions, assembly.

one wei = 10^-18 ether

szabo - 12

finney 15

gwei??

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

smart contracts can be written in solidity, vyper, LLL

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

# random notes

state of the dapps

dapp provider

DAU - daily active users

DEX, bancor, token store

unpkg allows you to use any node package without installing

how to tell if another address is a contract or regular address

ABIEncoderV2Pragma - enable experimental features

Wallet communicates with frontend on clientside. frontend communicates with server and an ethereum node. ethereum node communicates with smart contract on blockchain

inheritance

reentrancy attacks

hashing multiple values

generating random int

using libraries

a file can have multiple smart contracts

each instance of a smart contract has an address on the block chain

.on(confirmation???)

accessed externally - this.??
# Permissioned Blocks White Paper
## Introduction

## Smart Contract Model

A very simple model of a smart contract can be shown as having a set of functions with internal state.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

A smart contract function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending input parameters of a function in a transaction message to the network. The transaction is validated by network and upon consensus the new state becomes a permanent part of the blockchain.

The disadvantage of storing smart contract state information directly on a blockchain (such as Ethereum) and distributed to all nodes are:

- <b> No privacy.</b> The state information is stored on the blockchain's internal storage (Ethereum - a [Merkle Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)) is visible for all to see. The input parameters for functions sent in transactions to the network are also in clear view for all to see.
- <b> Cost. </b> Users are limited by the amount gas they are prepared to pay for the data that is sent for processing and storing on the network.

## Separating State from Functional Behaviour

If we separate the storage of the smart contract's state from its functional behaviour, we can then create a security model for ensuring the privacy of the contract's state information and at the same time significantly reduce the cost of data storage. 

To do this we need to write the smart contract functions as being [pure functions](https://en.wikipedia.org/wiki/Pure_function). In functional programming a pure function by design does not have the side-effect of storing state information. With Ethereum it is possible to so using the Solidity programming language which is shown in an example below.

To use a functional programming pattern when writing smart contracts functions, the previous contract state is combined with the current input parameters of a function to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - An initial event, with parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. Another contract transaction occurs that has the input parameter P2 and is combined with S1 to produce the new state S2.
</p>

```
function calculateTotal(uint total, uint tax, uint price, uint quantity, uint taxRate) returns (uint total, uint tax) {
      total = total + price * quantity;
      tax = tax + total * taxRate / 100
  }
```

<p align="center">
<b>Solidity pure function</b> - A function that calculates the running balance of total and tax. The state information is total and tax. The input parameters are price, quantity and taxRate.
</p>

## The Statechain

In order to retain the decentralisation properties of a blockchain system, we need to record the smart contract state changes on a decentralised storage system, IPFS was selected for the following reasons:
- Has a decentralised p2p file sharing algorithm called BitSwap
- The functionality to store data structures in object form - [IPLD](https://github.com/ipld/specs)
- Has streaming capabilities of large digital media formats (musics, movies etc).

To store the history of the smart contract state changes, a linked list data structure is chosen such that each state change references the previous state. This we will call a <b>statechain</b>.

By harnessing IPFS addressing, the statechain has the same immutability feature of a blockchain. IPFS uses a content addressing scheme such that the address of a block of data is the hash of that data. If we make the links of the statechain to be address references of IPFS blocks then if any state change recorded in the statechain were to be modified then this would mean the statechain address stored in the smart contract would not match.  

This useful property means that we only need to store the IPFS address of the last state change on the smart contract in oder to know that the addresses the full untampered history of the contract's state. We can then follow the linked references to resolve the complete statechain.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The smart contract only references the latest state change IPFS address.
</p>

Benefits:
- <b> Privacy</b> - The statechain address is the result of a one-way hashing function of the state information. Determination of the state information from the address alone cannot be calculated. Only by resolving the content of the address using the IPFS protocol can the state information be known. The Permissioned Blocks security model described below, extends the IPFS protocol with a security layer to protect resolving IPFS addresses by unauthorised actors.
- <b> Cost</b> - By storing only the statechain address on the blockchain significantly reduces the storage costs to negligible in comparison to storing the complete state history of the smart contract in the blockchain.

## IPFS as Decentralised Storage

## Statechain Security Model

Resolving the statechain requires both authentication and authorisation. Authentication that the request message was sent by the requestor and verificaton that the requestor is authorised to access the statechain. 

- <b> Authentication</b> - occurs by harnessing the blockchain's account features. A requestor proves the authenticity of the message being sent via a digital signature. The digital signature is produced using a blockchain account that they own.
- <b> Authorisation</b> - occurs through a verification that the requestor is authorised to access the statechain information. This is performed by checking with the smart contract that is linked to the statechain that the requestor is authorised. The smart contract stores a mapping of blockchain accounts to capabilities. These capabilities specify permissions to access the statechain and to execute various smart contract functions.


## Secure Transmisson of State Information

The statechain is encrypted using a shared contract key. The contract key is boxed using the public key of the person being granted a capability and stored at an IPFS address that only they can access.

The contract key is an asymmetric key used for encrypting and decrypting the statechain. The public key that is used for encrypting is stored with the contract's metadata and the private key used for decrypting is shared only with those that are granted access to the statechain. Sharing of the private key occurs by a method of boxing, that is, encrypting the contract key using the public key of the person being granted access. The boxed key is then stored at an IPFS address that can only be accessed by using a signed token.

<p align="center">
<img src="/images/permissioned-blocks-capabilities.png">
<br>
<b>Permissioned Blocks</b> - Bob requests state S2 from Alice via the modified IPFS Bit Swap protocol for Permissioned Blocks. Before sending, Alice's device authenticates Bob's identity and verifies if he is authorised to receive the data. Upon authorisation the data is encrypted using Alice's contract key and sent to Bob. Bob decrypts the data using his version of the contract key. 
</p>

## IPFS Token Authentication

The folked version of IPFS uses token authentication during the IPFS Bitswap algorithm to determine whether a request for an IPFS data block is permitted. The token is similar to a Javascript Web Token (JWT) employed in existing authentication systems used on the internet today. The token is divided into two segments, the first segment contans the issuer, capabilities and nonce information, and last segment contains the digital signature. The token's signature is generated using the blockchain account of the requestor. 

## Permissioned Blocks

The folked verson of IPFS has been extended to tag certain blocks as being <b>Permissioned Blocks</b>. Permissioned blocks require the authorisation in order to access the requested block.

Blocks are tagged in the IPFS datastore as being Permissioned Blocks by storing the smart contract's blockchain address along with the block data. When a request is made to retrieve a block from the datastore, if it is tagged, then the authorisation needs to occur. 

Authorisation occurs by a remote call from IPFS to the blockchain to query the smart contract that at the address specified by the tagged block. The remote procedure then queries the smart contracts capability hash map to verify whether the requestor is authorised to access the IPFS block. If authorised, the block is sent to the requestor. 

<p align="center">
<img src="/images/permissioned-block.png">
<br>
<b>IPFS Bitswap Authorisation</b> - A request is made from Bob's IPFS node to Alice's IPFS node for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's node makes a secure remote call to her instance of the blockchain to verify that Bob is authorised to access this block.
</p>

When the requestor receives the IPFS block, it is also tagged in their datastore as a Permissioned Block so that the same authorisation logic is used when others make a request for this block.

If the requestor is not authorised, then request is simply ignored. The IPFS DHT router system will then look elsewhere by querying other  nodes if they have the block. If all other nodes in the network either do not have the block, or the requestor is not authorised, then the block will not be resolved and a timeout will occur. When the timeout occurs, it will appear to the requestor as though the block simply does not exist.

## Statechain Validation

Since state information is not stored in the smart contract, validation is required in order to verify any changes recorded on the statechain are valid. In order to achieve this, an oracle called an Endorser is used to endorse state changes. The following simplified algorithm describes the proposing and endorsing behaviour. Consider the following solidity function:
```
function calculateCommission(uint balance, uint tax, uint commission) returns (uint balance, uint tax) {
      balance = balance + balance * commission / 100;
      tax = tax + balance * 20 / 100
  }
```
Using a functional programming pattern, this function has parameters <i>balance</i> and <i>tax</i>, which is state information, and <i>commission</i>, which is an input. The state information is <b>not</b> stored on the blockchain, so <i>balance</i> and <i>tax</i> are <b>not</b> member variables of the contract. This is why they are return by the function.

1. Bob retrieves the latest <i>balance</i> and <i>tax</i> variables from the statechain. 
2. He makes a <b>Call</b> to the function calculateCommission with the variables <i>balance</i>, <i>tax</i> and <i>commission</i>. The function returns the new <i>balance</i> and <i>tax</i> state information. <br><b>Note:</b> This is not a <i>Transaction</i> sent to the blockchain networ, it is a function call made on his own private blockchain node.
3. Bob updates the statechain by adding the new <i>balance</i> and <i>tax</i> state information and he also records the <i>commission</i> variable he used as an input. This generates an IPFS address of the statechain, which is a hash of the new history of state changes.
4. Bob saves the new statechain address in a member variable of the smart contract called <i>proposed_state</i>. He does this by sending a <i>Transaction</i> to the blockchain. 
5. The Endorser then calls the same calculateCommission function as Bob did, using the previous state information <i>balance</i> and <i>tax</i> and the <i>commission</i> input parameter Bob used to verify that the IPFS address stored in the <i>proposed_state</i> member variable is correct. If so, the Endorser then copies the <i>proposed_state</i> value to another member variable of the smart contract called <i>state</i> which holds the new verified IPFS address of the statechain.

## Contract Metadata

## Conclusion
## Citations

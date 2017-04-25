## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions with internal state.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to Function F2 modifies the internal state and produces an output. 
</p>

A smart contract function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending input parameters of a function in a transaction message to the network. The transaction is validated by network and upon network consensus the new state becomes a permanent part of the blockchain.

The issues with the above behaviour for storing the smart contract state information directly on the blockchain are:

- <b> No privacy.</b> The state information is stored on the blockchain's merkel tree are visible for all to inspect. Also, the input parameters of functions that are sent in the transaction are in clear view.
- <b> Cost. </b> The amount of data that can be sent in the transaction and stored in the blockchain's merketl tree is limited by the gas price for bytes sent and processed.

## Separating State from Functional Behaviour

If we separate the storage of the smart contract's state from its functional behaviour, we can then create a security model for protecting the state information and reduce the cost of storing data on the blockchain. 

To do this we need to program the functions of a smart contract to be [Pure Functions](https://en.wikipedia.org/wiki/Pure_function). Pure functions by design, do not have the side-effect of storing the state information during the execution of a function.

To employ a pure functional programming model with smart contracts, the previous contract astate is combined with the current input parameters of a function to produce the new contract state.

This new contract state is stored in a linked list structure called a <b>State Chain</b> on a decentralised storage system (a modified version of IPFS), and the hash address of the new state is sent as a transaction message to the network.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - An initial event, with parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. When another event occurs with the input parameter P2, this produces the new state S2.
</p>

## Privacy of the State Chain

The Permissioned Blocks security model is designed using a system of assigning capabilities that protect access to the Smart Contract's State Chain. The capabilities determine who can access and decrypt the state information and what contract functions they can execute.

The state information is encrypted with a shared contract key. The contract key is boxed using the public key of the person being granted access, and stored at an IPFS address that only they can access.

<b>Note:</b> A naive approach would be simply to encrypt the State Chain and publish on a public network. This approach however is vulnerable to the information being decrypted by brute force. For the same reason firewalls are employed today in computer networks, a better approch is to protect the information using a security model that limits access to the information.
 
<p align="center">
<img src="/images/permissioned-blocks-capabilities.png">
<br>
<b>Permissioned Blocks</b> - Bob requests state S2 from Alice via the modified IPFS Bit Swap module that uses token authentication. Before sending, Alice authenticates Bob's identity and checks the smart contract capabilities to verify that he is authorised to receive the data. Bob decrypts the data using his contract key. 
</p>

## Contract Key

The contract key is an asymmetric key used for encrypting and decrypting the contract's State Chain. The public key for encryption is stored in the contract's metadata and the private key used for decryption is shared only with those that have been granted access. Sharing of the private key occurs by a method of boxing, that is, encrypting the contract key using the public key of the person being granted access. The boxed key is then stored at an IPFS address that can only be accessed by using a signed token.

## IPFS Token Authentication

The modified version of IPFS uses token authentication in conjuction with the IPFS Bit Swap algorithm to determine whether a request for an IPFS data block should be distributed or not. The token is similar to a Javascript Web Token (JWT) employed in exisiting authentication systems used on the internet today. The token is divided into segments, with the last segment containing a digital signature of the person who created the token. The token signature is generated using the blockchain account of token creator. 

## Permissioned Blocks

The State Chain is stored in IPFS in block sizes up to 256 KB. Unlike standard IPFS blocks, State Chain blocks are coloured as being Permissioned Blocks. Permissioned Blocks colouring occurs by storing the address of the contract that the State Chain belongs to along side the block data. Meaning that they cannot be distributed.. 


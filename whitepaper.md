# Permissioned Blocks White Paper
## Introduction

## Smart Contract Model

We can model a smart contract as a [state machine](http://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine). A state machine that has a set of functions that produce deterministic outputs based upon input parameters and its internal state. 

A smart contract's state is modified by sending function input parameters as a transaction messages to the blockchain network. The transaction is validated by the network, and upon consensus, the new contract state becomes a permanent part of the blockchain. The output of the function can be obtained by any node in the network by quering the smart contract.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

The disadvantage of storing the smart contract state information directly on a blockchain (such as in Ethereum) are:

- <b> No privacy.</b> The internal storage that holds the state information is available for all nodes in the network to read. Also, the transactions that hold the input parameters to smart contract functions are available for all nodes to read. In Ethereum this is a [Merkle Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree) 
- <b> Cost. </b> In Ethereum there is a cost (the gas price) for processing and storing the data sent in transaction messages.

## Permissioned Based Distributed File Sharing

<p align="center">
<img src="/images/IPFS-file-sharing-1.png">
<br>
<b>Stage 1</b> - Node A owns contract and digital content.
</p>

<p align="center">
<img src="/images/IPFS-file-sharing-2.png">
<br>
<b>Stage 2</b> - Node B is authorised. Node A shares digital content with Node B.
</p>

<p align="center">
<img src="/images/IPFS-file-sharing-3.png">
<br>
<b>Stage 3</b> - Node D is authorised. Node A & Node B share digital content with Node D.
</p>


## Separating State Persistance from Functional Behaviour

If we separate the action of storing the smart contract's state from its functional behaviour, we can then store this information on an alternative storage system. With the data stored separately, we are then able to design a security model for ensuring the privacy of the smart contract state, and at the same time significantly reduce the blockchain storage costs.

To achieve this desired outcome, we need to program the functions of a smart contract as being [pure functions](https://en.wikipedia.org/wiki/Pure_function). A pure function, by definition in functional programming, does not have any side-effects. One such side-effect, is the persistence of state information. 

To apply a functional programming pattern to writing smart contracts, we combine the previous contract state, with the current input parameters of a function, to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - The first stage change is caused by the combination of input parameter P1 and the initial contract state of S0 to produce the new contract state S1. The second state change is caused by the combination of input parameter P2 and the previous state S1, to produce the new state S2.
</p>

Ethereum uses the Solidity programming language for writing smart contracts. It is possible to refactor a Solidity function to be a pure function, and thus not have state information persisted during execution. This is as shown in following example:

```
function calculateTotal(uint total, uint tax, uint price, uint quantity, uint taxRate) 
            returns (uint total, uint tax) {
      total = total + price * quantity;
      tax = tax + total * taxRate / 100
  }
```

<p align="center">
<b>Solidity pure function</b> - A function that calculates the running balance of <i>total</i> and <i>tax</i>. The state information are the variables <i>total</i> and <i>tax</i>. The input parameters are <i>price</i>, <i>quantity</i> and <i>taxRate</i>.
</p>

## The Statechain

If we store the smart contract state information on an alternative storage system, then in order to retain the benefits of the blockchain's distributed architecture, the storage system needs to also be distributed. For this and the following reasons, [IPFS](https://ipfs.io/) was selected:
- Uses a distributed p2p file sharing algorithm called BitSwap.
- Uses a content addressing scheme for resolving data.
- Can serve large digital content (books, music, movies etc).

To store the history of the smart contract state changes, a linked list data structure is chosen such that each state change references the previous state, and the head address of the linked list is stored in the smart contract. We will call this a <b>statechain</b>.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The smart contract only references the IPFS address of the latest state change.
</p>

IPFS uses a content addressing scheme for resolving data. This means that the address of a block of data is the hash of that data. By harnessing the IPFS addressing scheme, the statechain has the same immutability features of a blockchain. For example, if the statechain is linked together, such that each block links to the next using an IPFS address, then any modification to a block would cause the hash calculation for the statechain's head address to be different. This would result in statechain head address no longer matching the stored statechain address in the smart contract.

This useful property means that we need only to store the address of the lastest state change in the smart contract to be able to follow the linked references and resolve the complete statechain, knowing that we have the full untampered history of the smart contract's state.

Benefits:
- <b> Privacy</b> - The statechain address stored in the smart contract, is the result of a one-way hashing function of the statechain information. Determination of the statechain information cannot be calculated from the address alone. Only by resolving the content using the IPFS protocol, can the statechain information be known. 
- <b> Cost</b> - By storing the statechain address on the blockchain, significantly reduces the storage costs to negligible, in comparison to storing the complete state history on the blockchain.

## Statechain Validation

Validation of the statechain may need to be required in some user cases if the functions of the smart contract are intended to be executed by more than one blockchain account. For example, a smart contract function may expect 
```a + b = c```, 
but a malicious actor could claim to have executed the function and updates the statechain with the result as 
```a + b = z```. 
In this case validation by an oracle service should be employed to endorse the state changes. 

The following simplified algorithm describes the proposing and endorsing behaviour needed to validate the statechain. Consider the following solidity function:
```
function calculateCommission(uint balance, uint tax, uint commission) returns (uint balance, uint tax) {
      balance = balance + balance * commission / 100;
      tax = tax + balance * 20 / 100
  }
```
Using a functional programming pattern, this function has parameters <i>balance</i> and <i>tax</i>, which are state information, and <i>commission</i>, which is an input. The state information is <b>not</b> stored on the blockchain, so <i>balance</i> and <i>tax</i> are <b>not</b> member variables of the contract. This is why we see that they are return by the function.

1. Bob retrieves the latest <i>balance</i> and <i>tax</i> variables from the statechain. 
2. He makes a <b>Call</b> to the function calculateCommission with the variables <i>balance</i>, <i>tax</i> and <i>commission</i>. The function returns the new <i>balance</i> and <i>tax</i> state information. <br><b>Note:</b> This is not a <i>Transaction</i> sent to the blockchain network, it is a function call made on his own private blockchain node.
3. Bob updates the statechain by adding the new <i>balance</i> and <i>tax</i> state information and he also records the <i>commission</i> variable he used as an input. This generates an IPFS address of the statechain, which is a hash of the new history of state changes.
4. Bob saves the new statechain address in a member variable of the smart contract called <i>proposed_state</i>. He does this by sending a <b>Transaction</b> to the blockchain. 
5. The Endorser calls the same calculateCommission function as Bob did, using the previous state information <i>balance</i> and <i>tax</i> and the <i>commission</i> input parameter Bob. The Endorser stores the result in their instance of IPFS an verifies that the IPFS address stored in the <i>proposed_state</i> member variable is the same IPFS hash address. If so, the Endorser then copies the <i>proposed_state</i> value to another member variable of the smart contract called <i>state</i> which holds the new verified IPFS address of the statechain.

### Simple Contracts - No validation required
There is are use cases when validation of the statechain is not necessary. This is when the smart contract does not have functions that  calculate changes in state information. Instead, the purpose to the smart contract is to reference to digital content (e.g. a pdf file) that the owner of the contract would like to publish. 

### Multiple statechains
A smart contract could also be configured in a hybrid to have two or more statechains. For example, there may be one statechain that does not require validation, and other statechains that require validation by multiple oracles.

## Statechain Security Model

Permissioned Blocks extends the IPFS protocol with a security layer that protects the statechain from unauthorised access.

Resolving the statechain requires both authentication and authorisation of the requestor. That is, authentication that a message sent by a requstor for requesting statecahin information is authentic, and verificaton that the requestor is authorised to access the statechain. 

- <b> Authentication</b> - occurs by harnessing the blockchain's cryptography features. A requestor proves the authenticity of a message  by attaching a digital signature to the message being sent. The digital signature is produced and validated using the requestor's blockchain.
- <b> Authorisation</b> - occurs through verification that the requestor is authorised to access the statechain information. This is performed by quering the smart contract linked to the statechain. The smart contract stores a mapping of blockchain accounts to permissions. The permissions are capabilities to access the statechain and to execute smart contract functions.

<p align="center">
<img src="/images/authentication_authorisation.png">
<br>
<b>Authentication & Authorisation</b> - A signed request authenticates the requestor and the smart contract authorises the associated blockchain account as having permission to access the statechain and execute its functions.
</p>

## Token Authentication

Token authentication is used to prove the authenticity of message requesting statechain data. The token is similar to a [JSON Web Token](https://jwt.io/introduction/) (JWT) employed in existing authentication systems used on the internet today. The token is divided into two segments, the first segment contains claims, and last segment contains the digital signature. The token's signature is generated using the blockchain account of the requestor. 

```
{
      State Address: [IPFS Address]
      Blockchain: [Target Blockchain e.g. ETH]
      Account: [Blockchain account]
      Issuer: [Node ID]
      IssuedAt: [BlockNumber.TransactionNumber.TransactionHash]
}
```
<p align="center">
<b>Claims</b> - The first section of the token are the claims asserted by the requestor in JSON format and then Base64Url encoded. 
</p>

In order to produce a reliable timestamp for the <i>IssuedAt</i> claim, the blockchain itself is used as a reference point in time. Usually a timestamping authority is used in such distributed architectures to establish a unified reference point in time, such as in PKI systems. This however this would create a dependency on a third party, and the blockchain transaction count is a reliable, trustworthy heartbeat by design. The timestamp is generated by using latest Block Number and Transaction Number in combination this with the associated Transaction Hash.  

<p align="center">
<img src="/images/digital-signing.png">
<br>
<b>Digital Signing</b> - The second section of the token is the digital signature for the claims. The public key of the requestor is used to generate the digital signature. An Ethereum account is derived by the associated public key.
</p>

## Permissioned Blocks Authorisation

IPFS has been extended such that certain blocks, known as <b>Permissioned Blocks</b>, require authorisation to be resolved.

IPFS divides and stores data in block sizes of 256KB. To set apart permissioned blocks from regular blocks, permissioned blocks are tagged in the IPFS datastore with the smart contract's blockchain address. When a request is made to retrieve a block from the datastore, if it is tagged, then the security procedures of authentication and authorisation need to occur. 

Authorisation occurs by a remote call from IPFS to the blockchain to query the smart contract specified by the tagged block. The smart contract verifies whether the requestor is authorised to access the IPFS block. If authorised, the IPFS sends the block to the requestor via the IPFS bitswap protocol. 

<p align="center">
<img src="/images/permissioned-block-request.png">
<br>
<b>IPFS Bitswap Authorisation Protocol</b> - A request is made from Bob's IPFS instance to Alice's IPFS instance for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's IPFS instance makes a remote call to her blockchain instance to verify if Bob is authorised to access the requested block.
</p>

If the requestor successfully receives the IPFS block, it is likewise tagged in the datastore as a Permissioned Block and the same authorisation logic occurs when other node peers request this block.

If the requestor is not authorised, then request is simply ignored. The IPFS DHT router system will need to look elsewhere by querying other  nodes if they have the block. The routing system will continue to search for the requested block until a timeout occurs on the requestor's node. When the timeout occurs, this will signal to the requestor that the block cannot be resolved either because the block does not exist or they do not have permission to access the block.

## Secure Transmisson Channel

The IPFS bitswap channel used for transmission of blocks between nodes must be secured in order to retain the confidentiality of the smart contract's state information. To secure the channel, all communications are encrypted, such that only authorised blockchain accounts can decrypt the information.

Communication of the statechain information is encrypted using a shared contract key. The contract key an asymmetric key that is generated when the smart contract is deployed to the blockchain network. The key has a public key for encryption of communications and a private key for decryption. The public key is stored with the smart contract's metadata and the private key is shared only with those that are granted access to the statechain. 

<p align="center">
<img src="/images/secure_comms.png">
<br>
<b>Secure communication channel</b> - The IPFS bitswap channel is secured by token authentication and the encryption of permissioned blocks using a shared contract key. 
</p>

Sharing of the contract's private key occurs by a method of boxing. For example, Alice is owner of a smart contract and wishes to grant Bob permission to the access state information of the contract. Bob generates an Ethereum account and gives Alice the public key for that account. Alice encrypts the contract's private key using Bob's public key to create a boxed key that only Bob can decrypt. Alice stores Bob's boxed key on her IPFS instance, tagging each IPFS block with the smart contract address, such that they become Permissioned Blocks. Alice then updates the smart contract, adding Bob's account and the address of the boxed key. Bob queries the smart contract to retrieve the IPFS address of the box key and then makes an IPFS request to resolve the key. Upon receiving the boxed key, he decrypts the message using his private key to obtain the contract's private key.  

<p align="center">
<img src="/images/boxing_contract_key.png">
<br>
<b>Shared Contract Key</b> - The contract key is passed from Alice to Bob via the process of boxing, encrypting the private key using Bob's public key. The IPFS bitswap protocol is used for transmission of the boxed key.
</p>

Alice can revoke access to Bob by generating a new contract key and encrypting all state changes going forward with this key. Alice would then update the smart contract by removing Bob's capabilities, and updating any other users that may have been granted permissions with the address of the new contract key.

## Contract Metadata

## Conclusion
## Citations

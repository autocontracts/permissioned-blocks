# Permissioned Blocks White Paper
## Introduction

## Smart Contract Model

We can model a smart contract as a state machine. A state machine that has a set of functions that produce deterministic outputs based upon input parameters and its internal state. 

A smart contract's state is modified by sending function input parameters as a transaction messages to the blockchain network. The transaction is validated by the network, and upon consensus, the new contract state becomes a permanent part of the blockchain. The output of the function is obtained by any node in the network by quering the smart contract.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

The disadvantage of storing smart contract state information directly on a blockchain (such as in Ethereum) are:

- <b> No privacy.</b> The internal storage (Ethereum - a [Merkle Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)) that holds the state information is available for all nodes in the network to read. Also, the transactions that hold the input parameters to smart contract functions are available for all nodes to read.
- <b> Cost. </b> In Ethereum there is a cost (the gas price) for processing and storing the data sent in transaction messages. 

## Separating State Persistance from Functional Behaviour

If we separate the action of storing the smart contract's state from its functional behaviour, we can then store this information on an alternative storage system. With the data stored separately, we are then able to design a security model for ensuring the privacy of the smart contract state, and at the same time significantly reduce the blockchain storage costs.

To achieve this desired outcome, we need to program the smart contract functions as being [pure functions](https://en.wikipedia.org/wiki/Pure_function). A pure function by definition in functional programming, does not have any side-effects. And the persistence of state information in functional programming is considered a side-effect. 

To apply a functional programming pattern to writing smart contracts, we combine the previous contract state, with the current input parameters of a function, to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - The parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. An another state change occurs, where the input parameter P2 is combined with S1, to produce the new state S2.
</p>

Ethereum uses the Solidity programming language for writing smart contracts. It is possible to refactor a Solidity function to be a pure funcation, and thus not persist state information. This is as shown in following example:

```
function calculateTotal(uint total, uint tax, uint price, uint quantity, uint taxRate) 
            returns (uint total, uint tax) {
      total = total + price * quantity;
      tax = tax + total * taxRate / 100
  }
```

<p align="center">
<b>Solidity pure function</b> - A function that calculates the running balance of <i>total</i> and <i>tax</i>. The state information is the variables <i>total</i> and <i>tax</i>. The input parameters are <i>price</i>, <i>quantity</i> and <i>taxRate</i>.
</p>

## The Statechain

In order to retain the benefits of decentralisation, the alternative storage for persisting the state changes needs to be also decentralised. For this and the following reasons, [IPFS](https://ipfs.io/) was selected:
- Uses a distributed p2p file sharing algorithm called BitSwap.
- Uses a content addressing scheme for resolving data.
- Can store data structures in object form - [IPLD](https://github.com/ipld/specs).
- Can serve large digital content (books, music, movies etc).

To store the history of the smart contract state changes, a linked list data structure is chosen such that each state change references the previous state. We will call this a <b>statechain</b>.

IPFS uses a content addressing scheme for resolving data. This means that the address of a block of data is the hash of that data. By harnessing the IPFS addressing scheme, the statechain has the same immutability features of a blockchain. For example, if the statechain is linked together, such that each block links to the next using an IPFS address, then any modification of a block would cause the calculation of the statechain's head address to be different. This would result in statechain head address no longer matching the stored statechain address in the smart contract.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The smart contract only references the IPFS address of the latest state change.
</p>

This useful property means that we need only to store the address of the lastest state change on the smart contract to be able to follow the linked references to resolve the complete statechain, and we know that we have the full untampered history of the smart contract's state changes.

Benefits:
- <b> Privacy</b> - The statechain address is the result of a one-way hashing function of the statechain information. Determination of the statechain information cannot be calculated from the address alone. Only by resolving the content using the IPFS protocol, can the statechain information be known. 
- <b> Cost</b> - By storing the statechain address on the blockchain, significantly reduces the storage costs to negligible, in comparison to storing the complete state history on the blockchain.

## IPFS as Decentralised Storage

## Statechain Security Model

Permissioned Blocks extends the IPFS protocol with a security layer that protects the statechain from unauthorised access.

Resolving the statechain requires both authentication and authorisation of the requestor. Authentication that message sent by the requstor for requesting access to the statecahin is authentic, and verificaton that the requestor is authorised to access the statechain. 

- <b> Authentication</b> - occurs by harnessing the blockchain's cryptography features. A requestor proves the authenticity of a message,  by attaching a digital signature to the message that they send. The digital signature is produced using the blockchain account of the requestor. The message is verified using the public key associated with that blockchain account that only the requestor could have created the message.
- <b> Authorisation</b> - occurs through verification that the requestor is authorised to access the statechain information. This is performed by quering the smart contract linked to the statechain. The smart contract stores a mapping of blockchain accounts to permissions. The permissions are capabilities to access the statechain and to execute smart contract functions.

<p align="center">
<img src="/images/authentication_authorisation.png">
<br>
<b>Authentication & Authorisation</b> - A signed request authenticates the requestor and the smart contract stores blockchain accounts that have permission to access the statechain and execute its functions.
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
<b>Claims</b> - The first section of the token are claims asserted by the requestor in JSON format and then Base64Url encoded. 
</p>

In order to produce a reliable timestamp for the <i>IssuedAt</i> claim, the blockchain itself is used as a reference in time. Usually a timestamping authority is used for timestaming such as in a PKI system, however this would create a dependency on a third party. The timestamp is generated by using latest Block Number and Transaction Number and combining this with the associated Transaction Hash.  

<p align="center">
<img src="/images/digital-signing.png">
<br>
<b>Digital Signing</b> - The second section of the token is the digital signature of the claims. The public key of the requestor is used to generate a digital signature of the claims. An Ethereum account is derived by an associated public key.
</p>

## Permissioned Blocks Authorisation

IPFS has been extended such that certain blocks, known as <b>Permissioned Blocks</b>, require authorisation to be resolved.

IPFS divides and stores data in block sizes of 256KB. To set apart permissioned blocks from regular blocks, permissioned blocks are tagged in the IPFS datastore with the smart contract's blockchain address. When a request is made to retrieve a block from the datastore, if it is tagged, then the security procedures of authentication and authorisation needs to occur. 

Authorisation occurs by a remote call from IPFS to the blockchain, querying the smart contract specified by the tagged block. The smart contract verifies whether the requestor is authorised to access the IPFS block. If authorised, the block is sent to the requestor. 

<p align="center">
<img src="/images/permissioned-block-request.png">
<br>
<b>IPFS Bitswap Authorisation</b> - A request is made from Bob's IPFS instance to Alice's IPFS instance for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's IPFS instance makes a remote call to her blockchain instance to verify if Bob is authorised to access this block.
</p>

When the requestor receives the IPFS block, it is also tagged in their datastore as a Permissioned Block so that the same authorisation logic is used when others make a request for this block.

If the requestor is not authorised, then request is simply ignored. The IPFS DHT router system will then look elsewhere by querying other  nodes if they have the block. If all other nodes in the network either do not have the block, or the requestor is not authorised, then the block will not be resolved and a timeout will occur. When the timeout occurs, it will appear to the requestor as though the block simply does not exist.

## Secure Transmisson of Permissioned Blocks

The statechain is encrypted using a shared contract key. The contract key is boxed using the public key of the person being granted a capability and stored at an IPFS address that only they can access.

The contract key is an asymmetric key used for encrypting and decrypting the statechain. The public key that is used for encrypting is stored with the contract's metadata and the private key used for decrypting is shared only with those that are granted access to the statechain. Sharing of the private key occurs by a method of boxing, that is, encrypting the contract key using the public key of the person being granted access. The boxed key is then stored at an IPFS address that can only be accessed by using a signed token.

<p align="center">
<img src="/images/permissioned-blocks-capabilities.png">
<br>
<b>Permissioned Blocks</b> - Bob requests state S2 from Alice via the modified IPFS Bit Swap protocol for Permissioned Blocks. Before sending, Alice's device authenticates Bob's identity and verifies if he is authorised to receive the data. Upon authorisation the data is encrypted using Alice's contract key and sent to Bob. Bob decrypts the data using his version of the contract key. 
</p>

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
2. He makes a <b>Call</b> to the function calculateCommission with the variables <i>balance</i>, <i>tax</i> and <i>commission</i>. The function returns the new <i>balance</i> and <i>tax</i> state information. <br><b>Note:</b> This is not a <i>Transaction</i> sent to the blockchain network, it is a function call made on his own private blockchain node.
3. Bob updates the statechain by adding the new <i>balance</i> and <i>tax</i> state information and he also records the <i>commission</i> variable he used as an input. This generates an IPFS address of the statechain, which is a hash of the new history of state changes.
4. Bob saves the new statechain address in a member variable of the smart contract called <i>proposed_state</i>. He does this by sending a <i>Transaction</i> to the blockchain. 
5. The Endorser then calls the same calculateCommission function as Bob did, using the previous state information <i>balance</i> and <i>tax</i> and the <i>commission</i> input parameter Bob used to verify that the IPFS address stored in the <i>proposed_state</i> member variable is correct. If so, the Endorser then copies the <i>proposed_state</i> value to another member variable of the smart contract called <i>state</i> which holds the new verified IPFS address of the statechain.

## Contract Metadata

## Conclusion
## Citations

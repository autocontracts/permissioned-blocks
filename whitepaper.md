# Permissioned Blocks White Paper

## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions with internal state.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

A smart contract function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending input parameters of a function in a transaction message to the network. The transaction is validated by network and upon network consensus the new state becomes a permanent part of the blockchain.

Issues with this standard method of storing the smart contract state information directly on the blockchain are:

- <b> No privacy.</b> The state information that is stored on the blockchain's merkel tree is visible for all to see. The input parameters sent to the functions are are also in clear view for all to see.
- <b> Cost. </b> Users are limited to the amount of data that can be sent in the transaction and stored on the blockchain due to the gas cost of processing and storing bytes.

## Separating State from Functional Behaviour

If we separate the storage of the smart contract's state from its functional behaviour, we can then create a security model for ensuring the privacy of the contract's state information and significantly reduce the cost of data storage. 

To do this we need to program the smart contract functions as being [pure functions](https://en.wikipedia.org/wiki/Pure_function). In functional programming a pure function by design does not have the side-effect of storing state information during the execution.

To use a functional programming model with smart contracts, the previous contract state is combined with the current input parameters of a function to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - An initial event, with parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. When another contract transaction occurs that has the input parameter P2, this combined with S1 to produce the new state S2.
</p>

## The Statechain

So rather than the contract's current state information and history being stored on the blockchain, this state information is instead stored on IPFS, a decentralised storage system. A linked list data structure is used for storing the state information such that each  change in state references the previous state. 

IPFS blocks are content addressed, which means that the IPFS address is made by hashing a block's content. Therefore the statechain has the same useful feature of a blockchain, in that, if the content of any statechain block were to change then the hash of the head address would also change. This means that the smart contract only needs to store the IPFS address of the last state change to know that it has the full untampered history of the contract's state. Following all the referenced IPFS addresses in the linked list will resolve the complete statechain.

By only storing the latest statechain IPFS address in the contract significantly reduces the contract storage costs to be negligible in comparison to storing the complete state history. Also from the viewpoint of just the blockchain, the hash address of the statechain does not reveal any information about its content.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The smart contract only references the latest state change IPFS address.
</p>

## Privacy of the Statechain

Resolving the statechain requires being authenticated and authorised. Users authenticate who they are using a blockchain account and authorisation is handled by capabilities that are granted to users. 

The capabilites are stored on the smart contract as a hash map of the user's blockchain account. The capabilities are permission to access the statechain and to execute specified smart contract functions. 

The statechain is encrypted using a shared contract key. The contract key is boxed using the public key of the person being granted a capability and stored at an IPFS address that only they can access.
 
<p align="center">
<img src="/images/permissioned-blocks-capabilities.png">
<br>
<b>Permissioned Blocks</b> - Bob requests state S2 from Alice via a modified IPFS Bit Swap module that has the Permissioned Blocks Manager. Before sending, the Permissioned Blocks Manager on Alice's device authenticates Bob's identity and looks up the smart contract hash map to verify that Bob is authorised to receive the data. Upon authorisation the data is sent to Bob how decrypts the data using his version of the contract key. 
</p>

<b>Note:</b> A naive approach would be to only encrypt the satatechain without any access control measures in place. This approach however is vulnerable to the information, that would be public for all to see, being decrypted by brute force. For the same reason why firewalls are employed in computer networks today, a better approach is to design a security model that limits access to the information being protected.

## Contract Key

The contract key is an asymmetric key used for encrypting and decrypting the statechain. The public key that is used for encryption is stored with the contract's metadata and the private key used for decryption is shared only with those that are granted access to the statechain. Sharing of the private key occurs by a method of boxing, that is, encrypting the contract key using the public key of the person being granted access. The boxed key is then stored at an IPFS address that can only be accessed by using a signed token.

## IPFS Token Authentication

The modified Permissioned Blocks version of IPFS uses token authentication in conjunction with the IPFS Bitswap algorithm to determine whether a request for an IPFS data block is permitted to be distributed. The token is similar to a Javascript Web Token (JWT) employed in existing authentication systems used on the internet today. The token is divided into two segments, the first segment contans the issuer, capabilities and nonce information, and last segment contains the digital signature. The token's signature is generated using the blockchain account the user making the request. 

## Permissioned Blocks

IPFS divides and stores data in block sizes of 256 KB. The Permissiond Blocks version of IPFS has extended this functionality to tag certain blocks as being Permissioned Blocks and other as standard IPFS blocks. Permissioned blocks require the requestor to be authorised in order to receive the block being requested.

Blocks are tagged in the IPFS datastore as being Permissioned Blocks by storing the smart contract's blockchain address with the block data. When a request is made to retrieve a block from the datastore, if it is tagged then the authorisation needs to occur. 

Authorisation occurs by a remote call from IPFS to the blockchain in order to query the smart contract that is located at the address specified by the tagged block. The remote procedure then queries the smart contracts capability hash map to verify whether the requestor is authorised to access the IPFS block. If authorised the block is sent to the requestor. 

<p align="center">
<img src="/images/permissioned-block.png">
<br>
<b>IPFS Bitswap Authorisation</b> - A request is made from Bob's IPFS node to Alice's IPFS node for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's node makes a secure remote call to her instance of the blockchain to verify that Bob is authorised to access this block.
</p>

When the requestor receives the IPFS block, it is also tagged in their datastore as a Permissioned Block so that the same authorisation logic is used when others make a request for this block.

If the requestor is not authorised, then request is simply ignored. The IPFS DHT router system will then look elsewhere by querying other IPFS nodes if they have the block. If all other IPFS nodes in the network either do not have the block, or the requestor is not authorised, then the block will not be resolved and a timeout will occur. When the timeout occurs, it will appear to the requestor as if the block simply does not exist on the network.

## Statechain Validation

Since state information is not stored in the smart contract, validation is required in order to verify that any changes recorded on the statechain are valid. In order to achieve this, an oracle called an Endorser is used to endorse state changes. The following simplified algorithm describes the proposing and endorsing behaviour used. Consider the following solidity function:
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

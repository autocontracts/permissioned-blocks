## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions with internal state.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to Function F2 modifies the internal state and produces an output. 
</p>

A smart contract function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending input parameters of a function in a transaction message to the network. The transaction is validated by network and upon network consensus the new state becomes a permanent part of the blockchain.

Issues with the standard model of storing the smart contract state information directly on the blockchain are:

- <b> No privacy.</b> The state information is stored on the blockchain's merkel tree are visible for all to inspect. Also, the input parameters of functions that are sent in the transaction are in clear view.
- <b> Cost. </b> There is a limit to the amount of data that can be sent in the transaction and stored in the blockchain's merketl tree by the gas price that a users is prepared to spend for bytes sent and processed.

## Separating State from Functional Behaviour

If we separate the storage of the smart contract's state from its functional behaviour, we can then create a security model for ensuring privacy of the contract's state information and reduce the cost of storing data on the blockchain. 

To do this we need to program the smart contract functions as being [pure functions](https://en.wikipedia.org/wiki/Pure_function). In functional programming a pure function by design, does not have the side-effect of storing state information during the execution.

To use a functional programming model with smart contracts, the previous contract state is combined with the current input parameters of a function to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - An initial event, with parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. When another event occurs with the input parameter P2, this produces the new state S2.
</p>

## The Statechain

The statechain is stored on a decentralised storage system that is a modified version of IPFS. The statechain has a linked list structure such that each state has a reference to the previous state's address.

IPFS blocks are content addressed, which means that the IPFS address is a hash of the block's content. Therefore the statechain has the same useful feature of a blockchain, in that, if the content of any statechain block were to change then the hash of the head address would also change. This means that the smart contract only needs to store the IPFS address of the last state change without needing to store the entire set of data. Following all the referenced IPFS addresses in the linked list will resolve the complete statechain.

By only storing the latest statechain IPFS address in the contract significantly reduces the contract storage costs to become negligible compared to storing the complete state history. Also from the viewpoint of the blockchain, inspecting the hash address of the statechain without resolving the IPFS data does not reveal any information about the content of the statechain.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The head address is referenced by the smart contract
</p>

## Privacy of the Statechain

Resolving the statechain data requires being authenticated and authorised to access the statechain. Users need to be granted capabilities in order to access the statechain and decrypt the statechain's contents. Capabilities are also used to grant authorisation to execute specific smart contract functions.

User capabilites are stored on the smart contract as a hash map of the user's blockchain account.  

The statechain is encrypted using a shared contract key. The contract key is boxed using the public key of the person being granted a capability and this is stored at an IPFS address that only they can access.

<b>Note:</b> A naive approach would be to only encrypt the Statechain and publish on a public network. This approach however is vulnerable to the information being decrypted by brute force. For the same reason firewalls are employed today in computer networks, a better approach is to design a security model that limits access to the information.
 
<p align="center">
<img src="/images/permissioned-blocks-capabilities.png">
<br>
<b>Permissioned Blocks</b> - Bob requests state S2 from Alice via the modified IPFS Bit Swap module that uses token authentication. Before sending, Alice authenticates Bob's identity and checks the smart contract capabilities to verify that he is authorised to receive the data. Bob decrypts the data using his contract key. 
</p>

## Contract Key

The contract key is an asymmetric key used for encrypting and decrypting the contract's Statechain. The public key used for encryption is stored as part of the contract's metadata and the private key used for decryption is shared only with those that have been granted access. Sharing of the private key occurs by a method of boxing, that is, encrypting the contract key using the public key of the person being granted access. The boxed key is then stored at an IPFS address that can only be accessed by using a signed token.

## IPFS Token Authentication

The modified version of IPFS uses token authentication in conjuction with the IPFS Bitswap algorithm to determine whether a request for an IPFS data block should be distributed or not. The token is similar to a Javascript Web Token (JWT) employed in exisiting authentication systems used on the internet today. The token is divided into segments, with the last segment containing a digital signature of the person who created the token. The token signature is generated using the blockchain account of token creator. 

## Permissioned Blocks

IPFS divides and stores the contract's statechain in block sizes of 256 KB. Unlike standard IPFS blocks, the statechain blocks are <i>tagged</i> to indicate that they are Permissioned Blocks. Tagging occurs by storing the smart contract's blockchain address with the block data in the IPFS datastore. When a request is made to retrieve an IPFS block from the IPFS datastore that has been tagged, additional logic is executed to verify that the requestor is authorised to receive this block. 

Authorisation occurs by IPFS making a remote request to the smart contract address of the tagged block. The remote procedure then queries the smart contracts capability hash map to verify whether the requestor is authorised. If authorised the block is sent to the requestor. The requestor then also stores the received IPFS block as a tagged Permissioned block and uses the same security model for request that they receive for this block.

If the requestor is not authorised, then request is ignored. The IPFS DHT router will then have to look elsewhere. If all other IPFS nodes in the network either do not have the block, or the requestor is not authorised, then the address will not be resolved and a timeout will occur. When the timeout occurs, it will appear to the requestor as if the block simply does not exist.

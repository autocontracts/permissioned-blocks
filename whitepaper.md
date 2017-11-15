
<h1 align="center">Permissioned Blocks</h1>
<h2 align="center">A Design for Blockchain Privacy & Confidentiality</h2>
<p align="center">
By <a href="https://mikesmo.github.io/">Michael Smolenski</a>
</p>

## Abstract

This paper describes a protocol called Permissioned Blocks that enables privacy and confidentiality of smart contract transactions. A capability system is created for granting specific permissions to parties of a smart contract. This allows one or more parties who are the owner(s) of the smart contract give consent to other participants to access the confidential smart contract information and perform functional operations.

This protocol also enables files to be linked to a smart contract with the smart contract controlling access. The data is stored in a decentralised secure vault that is a modified version of IPFS allowing for the peer-to-peer distribution of the content. A benefit of this configuration is that files of any size can be attached to a smart contract without being charged high blockchain (gas) fees.

## Background

In late 2015, I presented a blockchain prototype called Midasium[[1]](http://midasium.com/) at the finals of a Citibank competition. The aim of the prototype was to show how blockchain technology could primarily improve cash flow management in the real estate industry and secondarily to create a Virtual Credit Bureau for managing portfolio risk of real estate in real time. Midasium went on to come 2nd place in a Santander Bank competition in 2016. In 2017 Deloitte wrote the paper "Blockchain in commercial real estate"[[2]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf) where the Midasium concept was presented to convey the potential of such blockchain models to disrupt the commercial real estate sector.

Although the vision of Midasium was appealing, one of the major technical challenges that needed further blockchain research and development was the lack of privacy and confidentiality of blockchain transaction. It was not an acceptable in a public blockchain scenario for the Midasium solution to display the amount of rent a tenant was paying publicly.

In 2016, I set out to do research and development to come up with a viable solution. This white paper and the accompanying prototype is the result of that work. 

Further work based on this paper, "External storage on the EVM: a static recompilation approach"[[4]](https://gist.github.com/shmookey/e1df57beeea0a2b8e6014bce6c69c0a4) by Luke Anthony Williams, describes a technique that abstracts away some of the complexities of writing smart contracts in a functional manner that are discussed in this paper. 

## Introduction

Blockchain technology with smart contracts[[5]](https://bitsonblocks.net/2016/02/01/a-gentle-introduction-to-smart-contracts/) promises the creation of new business models and economies. A major benefit is the efficiencies realised by participants transacting with one another in a peer-to-peer manner without requiring intermediaries.

However, blockchain technology is still in development and maturing, and there are some shortcomings that limit its practical use and uptake. One of such limitation is the lack of privacy and confidentiality of blockchain transactions[[6]](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/). Companies have compliance requirements to keep customer information private and they may also want to keep certain transactions private from competitors. 

There are also business use cases where confidentiality is important between parties. For example, in a medical situation, a patient may want to give consent to a set of physicians in order to access their health record but no one else.  

This white paper describes a protocol where private information that is controlled by a party can give consent to others to in order to access and use that information.

There are two categories of information that this protocol can be used for:
- To manage <b>smart contract</b> transactions -  data that is sent to a smart contract that is stored on a public blockchain.
- To manage <b>digital content</b> linked to a smart contract - E.g. documents, music, video...etc.

The blockchain described in this white paper is the Ethereum blockchain. However, the modular and decoupled design of Permissioned Blocks means that the technology can be used with any blockchain that has smart contract capabilities. That is a blockchain with network nodes that contain turing complete virtual machines[[9]](https://en.wikipedia.org/wiki/Turing_machine) for processing transactions.

A standard Ethereum smart contract stores its state information on the blockchain along with the smart contract’s instruction code. State changes of the smart contract can occur by transactions being transmitted publicly to every node in the network.

The Permissioned Blocks protocol gives additional functionality where state information that a party wishes to remain private and confidential is stored in a decentralised data store that has a security layer called the as opposed to the blockchain. This decentralised data store is called a decentralised secure vault. Rather than transmit transaction messages publicly to the blockchain network, only a reference address of the transaction that is stored in the secure vault is transmitted.
Those that are granted read permissions to the secure vault are authorised to access the smart contract’s private transactions.

With this design, the list of authorised users can be modified by the smart contract owner throughout the lifetime of the contract. When a user is added to the smart contract's list of authorised users, this user will have to access the full history of the transactions related to the smart contract. Likewise, when ownership changes hands, the new owner with inherit the full history of the smart contract.

A beneficial side effect of the Permission Block's protocol of storing transaction information in a decentralised data store is a reduction in the data stored on the public blockchain. It also means that the parties are not restricted in the size of the data that is transmitted as they will avoid gas fees for storage on the public blockchain.

Permissioned Blocks opens up the possibility for decentralised use cases that have until now not been possible as they have required privacy and confidentiality to be feasible, such as:

- Buying, selling and subscription of digital content (books, magazines, music, movies...)
- A distributed booking channel for the travel industry.
- Digital certificate registrations (Land titles, birth certificates, university qualifications...).
- Personal management of health records.
- Real estate; cash-flow management of rental properties[[13]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf).
- Know Your Customer (KYC) verification procedures for financial services.
- Improved credit scoring for financial services via real-time and complete credit & trade histories. 
- Trade finance, invoicing and payment records.
- Insurance history.
- and much more...

## High-level overview: A Permissioned File Sharing model for distributed networks.

Peer-to-peer file sharing protocols such as BitTorrent[[14]](https://en.wikipedia.org/wiki/BitTorrent) or IPFS[[24]](https://ipfs.io/) are distributed networks that freely share packets of data between nodes in their networks. If a node is interested in retrieving some data, an address is used for resolving where to find that data in the network. This address represents a routing path to the nodes that have a copy of the data or part of the dataset. There are no rules preventing requests for data from any address.  

Blockchain protocols broadcast all data that is created by a node in the form of a transaction to every other node in their network. There is no need for an addressing mechanism for retrieving data from a node as in a peer-to-peer file sharing protocol.

Blockchain protocols such as Ethereum[[7]](https://www.ethereum.org/) have the ability for any node to deploy a tamper-proof program known as a Smart Contract to all nodes in the network. By design, the nodes in a blockchain network are certain that at a certain block number they will all have the same version of smart contract code.

To deploy a smart contract, a party that runs a node in the network is required to pay a fee from a blockchain account. The smart contract can be programmed that this account used for deployment is the owner of the smart contract and that only transactions sent from that account are permitted to execute certain functions.  This opens up the possibility for a party to transmit an updateable set of security rules as a smart contract to a distributed network that is only be controlled by that party.

Combining a peer-to-peer file sharing protocol with a blockchain protocol, a smart contract can be used as the security mechanism for the peer-to-peer file sharing protocol. A security mechanism where the smart contract becomes the untampered source of truth that specifies the addresses of data that only certain parties are authorised to access. For example, if Alice transmits data to Bob, Alice can specify in a smart contract the other parties such as Carol that Bob is permitted to transmit the data to. Therefore Carol can request data from Bob without relying on only Alice to share the data.   

This creates a permissioned file sharing protocol where the rules for accessing content can be controlled along with a method of transferring value between parties in the form of cryptocurrency.

Benefits: 
- A security mechanism for controlling access to confidential data.
- No dependency on a centralised entity.
- Greater resilience to network failures and attacks due to its distributed architecture. 
- Scalability of bandwidth as demand increases.
- A low cost, self-sustaining network.

<p align="center">
<img src="/images/blockchain-inegration-layers.png">
<br>
<b>Protocol Layers</b> - The integration of blockchain protocol such as Ethereum and a file sharing protocol such as IPFS.
</p>

For example, consider an author of an e-book who would normally sell their work using an online marketplace such as Amazon[[15]](https://www.amazon.com/). Instead, they use a permissioned file sharing protocol where a smart contract is used for controlling distribution their e-book in exchange for a cryptocurrency[[16]](https://en.wikipedia.org/wiki/Cryptocurrency) payment. The centralised infrastructure that Amazon provides for hosting the content and managing the exchange or fiat currency for digital content would become redundant. The benefit for the author would be a substantial increase in revenue by removing the dependency and costs of using Amazon.

<p align="center">
<b>Distribution of Digital Content using a Smart Contract</b>
</p>

<p align="center">
<img src="/images/IPFS-file-sharing-1.png">
<br>
<b>Step 1</b> - Node A creates a smart contract that controls access to digital content on their node.
</p>
<br>
<p align="center">
<img src="/images/IPFS-file-sharing-2.png">
<br>
<b>Step 2</b> - In exchange for cryptocurrency, Node B is granted access. Node A then shares the digital content with Node B.
</p>
<br>
<p align="center">
<img src="/images/IPFS-file-sharing-3.png">
<br>
<b>Step 3</b> - In exchange for cryptocurrency, Node D is granted access. Node A & Node B share the digital content with Node D. Network throughput has increased, as two nodes are now serving the data to Node D.
</p>

## Privacy & Confidentiality for Smart Contracts

Smart Contracts are deployed to a blockchain by broadcasting a transaction that contains the program code from a node in the network. All nodes in the network will receive this transaction as part of an immutable block and as such the instruction code of the smart contract is also immutable. 

However, the state information of a smart contract is mutable and is modified by broadcasting additional transaction messages that are addressed to the smart contract. When these messages are broadcast, all nodes in the network will see this information. 

In order to protect sensitive smart contract state information from being seen and available to all nodes, a node instead stores transaction messages addressed to a smart contract in a decentralised data store. A data store that has a security layer that prevents access to data from unauthorised accounts. The node then broadcasts to the network the address of the transaction message to the network. These transaction addresses are stored in the smart contract in a queue data structure.

Any node that has been granted authorisation can resolve and access these set of transactions from the decentralised data store and replay them in order to obtain the current state of the smart contract.

## The Smart Contract State Machine

We can model a smart contract as a state machine[[17]](http://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine). A state machine that has a set of functions that produce deterministic outputs based upon input parameters and its internal state.

A smart contract's state is modified by sending input parameters for functions as a transaction messages to the blockchain network. The transaction is validated by network validators (miners in Ethereum), and upon consensus[[18]](https://medium.com/@chrshmmmr/consensus-in-blockchain-systems-in-short-691fc7d1fefe), the new state becomes a permanent part of the blockchain. The output of any function can be obtained by any node by querying the smart contract.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

The disadvantage of storing the smart contract state information directly on a blockchain in this manner are:

- <b> No privacy.</b> The parameters sent to smart contracts functions and the resulting state changes that are recorded in the internal data store (a Merkle Patricia Tree[[19]](https://github.com/ethereum/wiki/wiki/Patricia-Tree) in Ethereum) are available for all nodes in the network to read. 
- <b> Cost. </b> There is usually a transaction cost (in Ethereum a gas price[[20]](https://media.consensys.net/ethereum-gas-fuel-and-fees-3333e17fe1dc))) for processing and storing the data sent in messages.

## Separating State Persistence from Functional Behaviour

If we separate the recording of a smart contract's state changes from its functional behaviour, we are then able to store these state-changing messages on an alternative storage system. With the data stored separately, we are then able to build a security model that only allows a subset of nodes to access these messages, and at the same time significantly reduce the size of data stored on the blockchain.

To achieve this desired outcome, we need to program the functions of a smart contract as pure functions. A pure function[[21]](https://en.wikipedia.org/wiki/Pure_function), by definition in functional programming, does not depend on and does not modify the states of variables out of its scope[[22]](http://www.nicoespeon.com/en/2015/01/pure-functions-javascript/). This means a pure function will always return the same result for a given set of parameters.
```
var values = { a: 1 };

function impureFunction ( items ) {
  var b = 1;

  items.a = items.a * b + 2;

  return items.a;
}

var c = impureFunction( values );
// Now `values.a` is 3, the impure function modifies it.
```
<p align="center">
<b>An impure function</b> - A Javascript impure function that depends on the variable 'values' outside its scope. If run a second time with the same parameters, the result of this function will be different.
</p>

<p><br></p>

```
var values = { a: 1 };

function pureFunction ( a ) {
  var b = 1;

  a = a * b + 2;

  return a;
}

var c = pureFunction( values.a );
// `values.a` has not been modified, it's still 1
```
<p align="center">
<b>A Pure function</b> - A Javascript pure function that does not depend on a variable outside its scope. This function will always produce the same result for the same set of parameters.
</p>
<p><br></p>
To apply a functional programming pattern to writing smart contracts, we combine the previous contract state, with the current input parameters of a function, to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<b>A Smart Contract with Pure Functions</b> - The first state change is caused by the combination of input parameter P1 and the initial contract state of S0 to produce the new contract state S1. The second state change is caused by the combination of input parameter P2 and the previous state S1, to produce the new state S2.
</p>

<p><br></p>

Ethereum uses the Solidity[[23]](http://solidity.readthedocs.io/) programming language for writing smart contracts. It is possible to refactor a Solidity function to be a pure function, and thus not have state information persisted during execution of the function. This is as shown in following example:
```
contract ImpureSmartContract {
  uint public total; 
  uint public tax;
  
  function calculateTotal(uint price, uint quantity, uint taxRate) {
        total = total + price * quantity;
        tax = tax + total * taxRate / 100
   }
}
```
<p align="center">
<b>An impure function</b> - A Solidity impure function that calculates the running balance of <i>total</i> and <i>tax</i>. The state information are the variables <i>total</i> and <i>tax</i>. The input parameters are <i>price</i>, <i>quantity</i> and <i>taxRate</i>.
</p>

<p><br></p>

```
contract PureSmartContract {
  function calculateTotal(uint total, uint tax, uint price, uint quantity, uint taxRate) constant
              returns (uint total, uint tax) {
        total = total + price * quantity;
        tax = tax + total * taxRate / 100
   }
}
```
<p align="center">
<b>A pure function</b> - A Solidity pure function that calculates the running balance of <i>total</i> and <i>tax</i>. The state information are the variables <i>total</i> and <i>tax</i>. The input parameters are <i>price</i>, <i>quantity</i> and <i>taxRate</i>.
</p>

Here we see that the variables <i>total</i> and <i>tax</i> are returned by the function and their values are not persisted by the smart contract function.

## Recording State Changes

To protect confidential smart contract state information, any member variables that are required to be confidential are removed from the smart contract and the related functions are converted into confidential functions, by being made into pure functions where any sensitive variable are made as parameters to the function.

Transaction messages to confidential functions are not transmitted as blockchain transactions. Instead, these messages are recorded in a distributed secure vault. The secure vault is a modified version of IPFS[[24]](https://ipfs.io/) that has a security layer that prevents unauthorised access to content. IPFS has a content addressing scheme[[27]](https://en.wikipedia.org/wiki/Content-addressable_storage) for resolving data and protocol called BitSwap[[26]](https://github.com/ipfs/specs/tree/master/bitswap) for distributing data.

The reference addresses for each transaction message stored in the secure vault are broadcast to the network as blockchain transactions. These are processed using a function on the smart contract that adds the reference address in an array data structure. This way, the state-changing messages stored in the secure vault are linked to the smart contract. The function for adding the reference address will not process messages from unauthorised accounts. The ordering of the array is decided by the validators of the network by consensus. Note that the validators do not know and do not need to know the contents of reference addresses in order to validate the transactions.

Any node that has been granted access to the secure vault can retrieve the transaction messages and replay them in order to calculate the current state of the smart contract.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - An array of Reference Addresses to the Secure Vault
</p>

## The Distributed Secure Vault

The Secure Vault is an extension of the IPFS protocol with a security layer protecting access to data by unauthorised accounts.

Accessing data from the Secure Vault requires both authentication and authorisation by a requestor. Authentication that a message sent by a requester is authentic, and verification that the requestor is authorised to access the information. 

- <b> Authentication</b> - occurs by harnessing the blockchain's cryptography features. A requestor proves the authenticity of a message  by attaching a digital signature to the message being sent. The digital signature is produced and validated using the requestor's blockchain.
- <b> Authorisation</b> - occurs by verification that the requestor is authorised to access the requested address. The Secure Vault queries the smart contract that has a mapping of blockchain accounts with permissions for accessing the data.

<p align="center">
<img src="/images/authentication_authorisation.png">
<br>
<b>Authentication & Authorisation</b> - A signed request authenticates the requestor and the smart contract authorises the associated blockchain account as having permission to access the statechain and execute its functions.
</p>

## Token Authentication

Token authentication is used to prove the authenticity of message requesting data from the secure vault. The token is similar to a JSON Web Token (JWT)[[29]](https://jwt.io/introduction/) employed in existing authentication systems used on the internet today. The token is divided into two segments, the first segment contains claims, and last segment contains the digital signature. The token's signature is generated using the blockchain account of the requestor. 

```
{
      State Address: [IPFS Address]
      Blockchain: [Target Blockchain e.g. ETH]
      ChainID: [Main chain, test net]
      Account: [Blockchain account]
      Issuer: [Node ID]
      IssuedAt: [BlockNumber.TransactionNumber.TransactionHash]
}
```
<p align="center">
<b>Claims</b> - The first section of the token are the claims asserted by the requestor in JSON format and then Base64Url encoded. 
</p>

In order to produce a reliable timestamp for the <i>IssuedAt</i> claim, the blockchain itself is used as a reference point in time. Usually a timestamping authority[[30]](https://en.wikipedia.org/wiki/Trusted_timestamping) is used in such distributed architectures to establish a unified reference point in time, such as in PKI systems. This however this would create a dependency on a third party, and the blockchain transaction count is a reliable, trustworthy heartbeat by design. The timestamp is generated by using latest Block Number and Transaction Number in combination this with the associated Transaction Hash.  

<p align="center">
<img src="/images/digital-signing.png">
<br>
<b>Digital Signing</b> - The second section of the token is the digital signature for the claims. The public key of the requestor is used to generate the digital signature. An Ethereum account is derived by the associated public key.
</p>

## Permissioned Blocks Authorisation

IPFS is modified such that certain blocks, known as <b>Permissioned Blocks</b>, require authorisation to be resolved.

IPFS divides and stores data in block sizes of 256KB. To set apart permissioned blocks from regular blocks, permissioned blocks are tagged in the IPFS datastore with the smart contract's blockchain address. When a request is made to retrieve a block from the datastore, if it is tagged, then the security procedures of authentication and authorisation need to occur. 

Authorisation of the requestor to access a block occurs by verifying that the requestor has been granted a 'read' capability. The capabilities are stored in the smart contract and these are queried via a remote call from IPFS to smart contract specified by each tagged block. If authorised, the IPFS sends the block to the requestor via the IPFS bitswap protocol. 

<p align="center">
<img src="/images/permissioned-block-request.png">
<br>
<b>IPFS Bitswap Authorisation Protocol</b> - A request is made from Bob's IPFS instance to Alice's IPFS instance for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's IPFS instance makes a remote call to her blockchain instance to verify if Bob is authorised to access the requested block.
</p>

When a requestor successfully receives the IPFS block, it is then tagged in the datastore with the smart contracts address and the same authorisation logic occur will occur if any other node in the network requests this block.

If the requestor is not authorised, then request is simply ignored. The IPFS Distributed Hash Table (DHT) routing system will need to look elsewhere by querying other nodes if they have the block. The routing system will continue to search for the requested block until a timeout occurs on the requestor's node. When the timeout occurs, this will signal to the requestor that the block cannot be resolved either because the block does not exist or they do not have permission to access the block.

## User Capabilities

The user capabilites are stored in the smart contract and specify the permissions that have been granted to each user. When granting a capability to a user, the user's public key is stored with the capability being granted. A user can have multiple capabilities granted to them. The capabilities are defined as follows:

| Capability | Description                                                                                                           |
|------------|-----------------------------------------------------------------------------------------------------------------------|
| Write      | The user is able to add new state information.                                                       |
| Read       | The user is able to read state information. This capability is used in the authorisation process. |
| Grant      | The user is able to grant a capability to another user.                                                               |
| Revoke     | The is able to revoke a capability from another user.                                                                 |

Any capability can be revoked from a user by any user that has been granted the 'revoke' capability. When revoking occurs, the specified capability that was assigned to user is deleted from the smart contract.

## A Secure Transmission Channel

The IPFS bitswap channel used for transmission of blocks between nodes needs to be secured in order to avoid messages being read while in transit. To secure the channel, all communications are encrypted, such that only the receiver can decrypt the information being sent.

Communication of the information is encrypted by the sender using the receiver's public key and decrypted by the receiver using their private key. The receiver's public key is part of an asymmetric key that the receiver generates. The asymmetric key has a public key element for encryption of communications and a private key for decryption. The public key is stored in the smart contract and the private key is kept secret by the receiver and is never transmitted.

<p align="center">
<img src="/images/secure_comms.png">
<br>
<b>Secure communication channel</b> - The IPFS bitswap channel is secured by token authentication and the encryption of permissioned blocks using a shared contract key. 
</p>

All state information is stored encrypted within the IPFS datastore to ensure the full security of the data. When a user writes information, those blocks are encrypted using their asymmetric key. When a requestor receives a block, they store the information in encrypted state as they have received it. Only when they send blocks to another requestor does the block get decrypted and then encrypted using the public key of the new requestor.

## Conclusion
In this paper we have introduced a new technology called Permissioned Blocks that is a combination of peer-to-peer file sharing and blockchain technology. Permissioned Blocks allows parties to conduct transactions on public blockchains whilst keeping sensitive information private and confidential.

<p align="center">
<img src="/images/permissioned-blocks-architechture-demo.png">
<br>
<b>Working Demo</b> - The architecture of a working demonstration of permissioned blocks.
</p>

## Citations
- [[1]](http://midasium.com/) "Midasium: Blockchain of Real Estate" http://midasium.com/
- [[2]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf) "Blockchain in commercial real estate" by Deloitte https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf
- [[3]](https://github.com/jpmorganchase/quorum-docs/blob/master/Quorum%20Whitepaper%20v0.1.pdf) "Quorum White Paper" by JP Morgan https://github.com/jpmorganchase/quorum-docs/blob/master/Quorum%20Whitepaper%20v0.1.pdf
- [[4]](https://gist.github.com/shmookey/e1df57beeea0a2b8e6014bce6c69c0a4) "External storage on the EVM: a static recompilation approach" by Luke Anthony Williams https://gist.github.com/shmookey/e1df57beeea0a2b8e6014bce6c69c0a4
- [[5]](https://bitsonblocks.net/2016/02/01/a-gentle-introduction-to-smart-contracts/) "A Gentle Introduction to Smart Contracts" by Antony Lewis https://bitsonblocks.net/2016/02/01/a-gentle-introduction-to-smart-contracts
- [[6]](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/) "Privacy on the Blockchain" by Vitalik Buterin https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/
- [[7]](https://www.ethereum.org/) Ethereum https://www.ethereum.org/
- [[8]](http://autocontracts.io/) Autonomous Contracts http://autocontracts.io/
- [[9]](https://en.wikipedia.org/wiki/Turing_machine) Turing Machine https://en.wikipedia.org/wiki/Turing_machine
- [[10]](http://www.learningmachine.com/) The Learning Machine http://www.learningmachine.com/
- [[11]](https://www.blockwise.org/2017/04/28/the-global-identity-on-blockchain/) Blockwise Global Identity https://www.blockwise.org/2017/04/28/the-global-identity-on-blockchain
- [[12]](https://en.wikipedia.org/wiki/Cryptographic_hash_function) One way hashing function https://en.wikipedia.org/wiki/Cryptographic_hash_function
- [[13]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf) "Blockchain in Commercial Real Estate" by Deloitte Financial Services https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf
- [[14]](https://en.wikipedia.org/wiki/BitTorrent) Bit Torrent https://en.wikipedia.org/wiki/BitTorrent
- [[15]](https://www.amazon.com/) Amazon https://www.amazon.com
- [[16]](https://en.wikipedia.org/wiki/Cryptocurrency) Cryptocurrency https://en.wikipedia.org/wiki/Cryptocurrency
- [[17]](http://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine) Solidity Smart Contract Patterns http://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine
- [[18]](https://medium.com/@chrshmmmr/consensus-in-blockchain-systems-in-short-691fc7d1fefe) "Consensus in Blockchain Systems. In Short." by Chris Hammerschmidt https://medium.com/@chrshmmmr/consensus-in-blockchain-systems-in-short-691fc7d1fefe
- [[19]](https://github.com/ethereum/wiki/wiki/Patricia-Tree) Merkle Patricia Tree Specification https://github.com/ethereum/wiki/wiki/Patricia-Tree
- [[20]](https://media.consensys.net/ethereum-gas-fuel-and-fees-3333e17fe1dc) "Ethereum Gas Fuel and Fees" by Joseph Chow https://media.consensys.net/ethereum-gas-fuel-and-fees-3333e17fe1dc
- [[21]](https://en.wikipedia.org/wiki/Pure_function) Pure Functions https://en.wikipedia.org/wiki/Pure_function
- [[22]](http://www.nicoespeon.com/en/2015/01/pure-functions-javascript/) Pure Functions in Javascript by Nicolas Carlo http://www.nicoespeon.com/en/2015/01/pure-functions-javascript/
- [[23]](http://solidity.readthedocs.io/) Solidity http://solidity.readthedocs.io/
- [[24]](https://ipfs.io/) Interplanetary File System (https://ipfs.io/)
- [[25]](https://arxiv.org/pdf/1407.3561v1.pdf) "IPFS - Content Addressed, Versioned, P2P File System" by Juan Benet https://arxiv.org/pdf/1407.3561v1.pdf
- [[26]](https://github.com/ipfs/specs/tree/master/bitswap) Bitswap Specs https://github.com/ipfs/specs/tree/master/bitswap
- [[27]](https://en.wikipedia.org/wiki/Content-addressable_storage) Content Adressable Storage https://en.wikipedia.org/wiki/Content-addressable_storage
- [[28]](https://ethereum.gitbooks.io/frontier-guide/content/interacting_contract.html) Interacting with Contracts https://ethereum.gitbooks.io/frontier-guide/content/interacting_contract.html
- [[29]](https://jwt.io/introduction/) JSON Web Tokens - Introduction https://jwt.io/introduction
- [[30]](https://en.wikipedia.org/wiki/Trusted_timestamping) Trusted timestamping https://en.wikipedia.org/wiki/Trusted_timestamping
- [[31]](https://raiden.network/) The Raiden Network https://raiden.network/

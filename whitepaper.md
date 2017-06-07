
<h1 align="center">Permissioned Blocks White Paper</h1>
<p align="center">
By <a href="https://mikesmo.github.io/">Michael Smolenski</a>
</p>

## Background

In late 2015, I presented a blockchain prototype called Midasium[[1]](http://midasium.com/) at the finals of a Citibank competition. The aim of the prototype was to show how blockchain technology could primarily improve cash flow management in the real estate industry, and secondarily would create a Virtual Credit Bureau to manage portfolio risk of real estate in real time. Midasium went on to come 2nd place in a Santander Bank competition in 2016. In 2017 Deloitte wrote the paper "Blockchain in commercial real estate"[[2]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf) where  the Midasium concept was presented to convey the potential of such blockchain models to disrupt the commerical real estate sector. 

Although the vision of Midasium was appealing, there still remained technical challenges with blockchain technology at the time that needed further research and development for the full potential of this technology to be realised. These were:
- <b>Scalability</b> - The throughput speeds and latency times of the leading blockchains were too slow for use cases that have large transactional volumes.
- <b>Privacy</b> - All information on a public blockchain is visible to all participants to read.

In 2016, I set out to do my own work on solving the privacy issue. This white paper and the accompanying prototype is the result of that work. Various other platforms such as Quorum[[3]](https://github.com/jpmorganchase/quorum-docs/blob/master/Quorum%20Whitepaper%20v0.1.pdf) by JP Morgan have also recently produced viable solutions in this area. However, I believe the design described in this white paper provides a different angle for solving the problem, and as such, there are some unique features and benefits when applied to various use cases.  

Further work based on this paper, "External storage on the EVM: a static recompilation approach"[[4]](https://gist.github.com/shmookey/e1df57beeea0a2b8e6014bce6c69c0a4) by Luke Anthony Williams, describes a technique that abstracts away some of the complexities of writing smart contracts in a functional manner that are discussed in this paper. 

## Introduction

Blockchain technology with smart contracts[[5]](https://bitsonblocks.net/2016/02/01/a-gentle-introduction-to-smart-contracts/) promise the creation of new business models and economies. A major benefit is efficiencies realised by participants transacting with one another in a peer-to-peer manner, without requiring intermediaries.

However, blockchain technology is still in development and maturing, and there are some shortcomings that limit the technology's practical use and uptake. One of such limitation is the lack of privacy[[6]](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/). For example, data recorded on a public blockchain, such as Ethereum[[7]](https://www.ethereum.org/), is available in the open and clear for all participants to read and interpret. This leaves blockchain technology in its current form, impractical for many use cases that require privacy and the security of confidential and personal information. 

This white paper describes technology developed by Autonomous Contracts[[8]](http://autocontracts.io/) called <b>Permissioned Blocks</b>. Using Permissioned Blocks, sensitive information can remain confidential and private between parties. A protoype of Permissioned Blocks has been developed, and you can find a working demonstration [here](https://www.youtube.com/watch?v=Zt9DIopmzbA) for further inspection.

<p align="center">
<img src="/images/blockchain-privacy.png">
<br>
<b>Permissioned Blocks</b> - A figurative representation of a private block of data being shared with an authorised user.
</p>

There are two categories Permissioned Blocks can be applied:
- To secure <b>smart contract</b> state information - internal state information recorded during a blockchain transaction.
- To secure <b>digital content</b> attached to a smart contract - E.g. Legal documents, music, video...etc.

The blockchain used and described in this white paper is the Ethereum blockchain. However, the modular and decoupled design of Permissioned Blocks, means that the technology can be used with any blockchain that has smart contract capabilities. That is, a blockchain with network nodes that contain turing complete virtual machines[[9]](https://en.wikipedia.org/wiki/Turing_machine) for processing transactions.

<p align="center">
<img src="/images/permissioned-blocks-architechture-demo.png">
<br>
<b>Working Demo</b> - The architecture of a working demonstration of permissioned blocks.
</p>

Many blockchain solutions[[10]](http://www.learningmachine.com/) [[11]](https://www.blockwise.org/2017/04/28/the-global-identity-on-blockchain/) link externally stored data to a blockchain using a unique fingerprint (known as a one-way hashing function[[12]](https://en.wikipedia.org/wiki/Cryptographic_hash_function)) of the data set. These solutions tend to rely on traditional centralised databases for the external storage, which in effect nullifies some of the unique characteristics of a distributed blockchain architecture.

Permissioned Blocks describes a decentralised model for linking external data to a public blockchain. External data that is <i>attached</i> to a smart contract and shared only with authorised accounts. Furthermore, computation of smart contract functions, such as calculation of bond payments, commissions and taxes, can also remain private using this technology. 

Some use cases are:
- Buying, selling and subscription of digital content (books, magazines, music, movies...)
- A distributed booking channel for the travel industry.
- Digital certificate registrations (Land titles, birth certificates, university qualifications...).
- Personal management of health records.
- Real estate; cash-flow management of rental properties[[13]](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/financial-services/us-fsi-rec-blockchain-in-commercial-real-estate.pdf).
- Know Your Customer (KYC) verification procedures for financial services.
- Improved credit scoring for financial services via real-time and complete credit & trade histories. 
- Trade finance, invoicing and payment records.
- Insurance history.
- and more...

## Permissioned Based File Sharing

Current peer-to-peer file sharing protocols such as BitTorrent[[14]](https://en.wikipedia.org/wiki/BitTorrent) are permissionless based protocols. By design, their distributed architectures have no central point of control, which means that once content is added to a network node, the data will be freely shared with any other node. Creators and owners of digital content are almost powerless to stop copyright material being distriubuted on such networks.

If we combine a peer-to-peer file sharing protocol with a blockchain protocol, this opens the possibility to harness the benefits of both protocols. That is, a low cost data sharing network with security control logic that protects access to copyright material and confidential information.

Benefits: 
- No dependency on a centralised service to control data access.
- More resilient to network failures or cyber attacks due to a distributed architecture. One node failure will not compromise the entire network.
- Improved security of confidential data.
- Higher data throughput as demand increases.
- A low cost, self-sustaining network.

<p align="center">
<img src="/images/blockchain-inegration-layers.png">
<br>
<b>Protocol Layers</b> - The integration of blockchain protocol such as Ethereum and a file sharing protocol such as IPFS.
</p>

For example, consider an author of an e-book who normally sells their work using an online publisher such as Amazon[[15]](https://www.amazon.com/). Instead, they use a permissioned based file sharing protocol where a smart contract is be used to distribute their e-book in exchange for a cryptocurrency[[16]](https://en.wikipedia.org/wiki/Cryptocurrency) payment. The minimum infrastructure the author would require to publish to a global audience would simply be an ordinary laptop. The benefit for the author would be a substantial increase in revenue by removing the dependency and costs of using the online publisher.

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

## The Smart Contract State Machine

We can model a smart contract as a state machine[[17]](http://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine). A state machine that has a set of functions that produce deterministic outputs based upon input parameters and its internal state. 

A smart contract's state is modified by sending function input parameters as a transaction messages to the blockchain network. The transaction is validated by the network, and upon consensus[[18]](https://medium.com/@chrshmmmr/consensus-in-blockchain-systems-in-short-691fc7d1fefe), the new contract state becomes a permanent part of the blockchain. The output of the function can be obtained by any node in the network by querying the smart contract.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>A Smart Contract Model</b> - Input to function F2 modifies the internal state and produces an output. 
</p>

The disadvantage of storing the smart contract state information directly on a blockchain are:

- <b> No privacy.</b> The internal storage that holds the state information is available for all nodes in the network to read. Also, the transactions that hold the input parameters to smart contract functions are available for all nodes to read. In Ethereum this is a Merkle Patricia Tree[[19]](https://github.com/ethereum/wiki/wiki/Patricia-Tree) 
- <b> Cost. </b> In Ethereum there is a cost (the gas price[[20]](https://media.consensys.net/ethereum-gas-fuel-and-fees-3333e17fe1dc)) for processing and storing the data sent in transaction messages.

## Separating State Persistence from Functional Behaviour

If we separate the action of storing the smart contract's state from its functional behaviour, we can then store this information on an alternative storage system. With the data stored separately, we are then able to design a security model for ensuring the privacy of the smart contract state, and at the same time significantly reduce the blockchain storage costs.

To achieve this desired outcome, we need to program the functions of a smart contract as being pure functions. A pure function[[21]](https://en.wikipedia.org/wiki/Pure_function), by definition in functional programming, does not depend on and does not modify the states of variables out of its scope[[22]](http://www.nicoespeon.com/en/2015/01/pure-functions-javascript/). That means a pure function always returns the same result given same parameters.
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
<b>An impure function</b> - A Javascript impure function
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
<b>A Pure function</b> - A Javascript pure function
</p>
<p><br></p>
To apply a functional programming pattern to writing smart contracts, we combine the previous contract state, with the current input parameters of a function, to produce the new contract state.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<b>A Smart Contract with Pure Functions</b> - The first state change is caused by the combination of input parameter P1 and the initial contract state of S0 to produce the new contract state S1. The second state change is caused by the combination of input parameter P2 and the previous state S1, to produce the new state S2.
</p>

<p><br></p>

Ethereum uses the Solidity[[23]](http://solidity.readthedocs.io/) programming language for writing smart contracts. It is possible to refactor a Solidity function to be a pure function, and thus not have state information persisted during execution. This is as shown in following example:
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

## The Statechain

If we store the smart contract state information on an alternative storage system, then in order to retain the benefits of the blockchain's distributed architecture, the storage system needs to also be distributed. For this, and the following reasons, IPFS[[24]](https://ipfs.io/), which is a Content Addressed, Versioned, P2P File System[[25]](https://arxiv.org/pdf/1407.3561v1.pdf) was selected:
- Uses a distributed p2p file sharing algorithm called BitSwap[[26]](https://github.com/ipfs/specs/tree/master/bitswap).
- Uses a content addressing scheme[[27]](https://en.wikipedia.org/wiki/Content-addressable_storage) for resolving data.
- Serves versioned digital content of any size.

To store the history of the smart contract state changes, a linked list data structure is chosen such that each state change references the previous state, and the head address of the linked list is stored in the smart contract. We will call this a <b>statechain</b>.

<p align="center">
<img src="/images/statechain.png">
<br>
<b>The Statechain</b> - A linked list of IPFS Hash Addresses. The smart contract only references the IPFS address of the latest state change.
</p>

IPFS uses a content addressing scheme for resolving data. This means that the address of a block of data is the hash of that data. By harnessing the IPFS addressing scheme, the statechain has the same immutability features of a blockchain. For example, if the statechain is linked together, such that each block links to the next using an IPFS address, then any modification to a block would cause the hash calculation for the statechain's head address to be different. This would result in statechain head address no longer matching the stored statechain address in the smart contract.

This useful property means that we need only to store the address of the latest state change in the smart contract to be able to follow the linked references and resolve the complete statechain, knowing that we have the full untampered history of the smart contract's state.

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
function calculateCommission(uint balance, uint tax, uint commission) constant
          returns (uint balance, uint tax) {
      balance = balance + balance * commission / 100;
      tax = tax + balance * 20 / 100
  }
```
Using a functional programming pattern, this function has parameters <i>balance</i> and <i>tax</i>, which are state information, and <i>commission</i>, which is an input. The state information is <b>not</b> stored on the blockchain, so <i>balance</i> and <i>tax</i> are <b>not</b> member variables of the contract. This is why we see that they are returned by the function.

1. Bob retrieves the latest <i>balance</i> and <i>tax</i> variables from the statechain. 
2. He makes a <b>Call</b>[[28]](https://ethereum.gitbooks.io/frontier-guide/content/interacting_contract.html) to the function calculateCommission with the variables <i>balance</i>, <i>tax</i> and <i>commission</i>. The function returns the new <i>balance</i> and <i>tax</i> state information. <br><b>Note:</b> This is not a <i>Transaction</i> sent to the blockchain network, it is a function call made to his own node.
3. Bob updates the statechain by adding the new <i>balance</i> and <i>tax</i> state information and he also records the <i>commission</i> variable he used as an input. This generates an IPFS address of the statechain, which is a hash of the new history of state changes.
4. Bob saves the new statechain address in a member variable of the smart contract called <i>proposed_state</i>. He does this by sending a <b>Transaction</b> to the blockchain. 
5. The Endorser calls the same calculateCommission function as Bob did, using the previous state information <i>balance</i> and <i>tax</i> and the <i>commission</i> input parameter Bob. The Endorser stores the result in their instance of IPFS and verifies that the IPFS address stored in the <i>proposed_state</i> member variable is the same IPFS hash address. If so, the Endorser then copies the <i>proposed_state</i> value to another member variable of the smart contract called <i>state</i> which holds the new verified IPFS address of the statechain.

### Simple Contracts: No statechain validation
Validation of the statechain is not necessary for use cases where the calculation of state change information does not occur. For example, in scenarios where the smart contract does not have any functions that are using state inforation to perform calculations. This may be in the case for smart contracts where the statechain only links to digital content (e.g. a pdf file) to the smart contract. The purpose of  the smart contract is to control how the digital content is distributed.

### Complex Contracts: Multi-statechain validation
A smart contract could be configured with a hybrid of two or more statechains. For example, depending upon the use-case, there may be one statechain requiring no validation, and other statechains requiring validation by multiple endorser-oracles.

## Statechain Security Model

Permissioned Blocks extends the IPFS protocol with a security layer that protects the statechain from unauthorised access.

Resolving the statechain requires both authentication and authorisation of the requestor. That is, authentication that a message sent by a requester for requesting statechain information is authentic, and verification that the requestor is authorised to access the statechain. 

- <b> Authentication</b> - occurs by harnessing the blockchain's cryptography features. A requestor proves the authenticity of a message  by attaching a digital signature to the message being sent. The digital signature is produced and validated using the requestor's blockchain.
- <b> Authorisation</b> - occurs through verification that the requestor is authorised to access the statechain information. This is performed by querying the smart contract linked to the statechain. The smart contract stores a mapping of blockchain accounts to permissions. The permissions are capabilities to access the statechain and to execute smart contract functions.

<p align="center">
<img src="/images/authentication_authorisation.png">
<br>
<b>Authentication & Authorisation</b> - A signed request authenticates the requestor and the smart contract authorises the associated blockchain account as having permission to access the statechain and execute its functions.
</p>

## Token Authentication

Token authentication is used to prove the authenticity of message requesting statechain data. The token is similar to a JSON Web Token (JWT)[[29]](https://jwt.io/introduction/) employed in existing authentication systems used on the internet today. The token is divided into two segments, the first segment contains claims, and last segment contains the digital signature. The token's signature is generated using the blockchain account of the requestor. 

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

In order to produce a reliable timestamp for the <i>IssuedAt</i> claim, the blockchain itself is used as a reference point in time. Usually a timestamping authority[[30]](https://en.wikipedia.org/wiki/Trusted_timestamping) is used in such distributed architectures to establish a unified reference point in time, such as in PKI systems. This however this would create a dependency on a third party, and the blockchain transaction count is a reliable, trustworthy heartbeat by design. The timestamp is generated by using latest Block Number and Transaction Number in combination this with the associated Transaction Hash.  

<p align="center">
<img src="/images/digital-signing.png">
<br>
<b>Digital Signing</b> - The second section of the token is the digital signature for the claims. The public key of the requestor is used to generate the digital signature. An Ethereum account is derived by the associated public key.
</p>

## Permissioned Blocks Authorisation

The Permissioned Blocks design extends IPFS such that certain blocks, known as <b>Permissioned Blocks</b>, require authorisation to be resolved.

IPFS divides and stores data in block sizes of 256KB. To set apart permissioned blocks from regular blocks, permissioned blocks are tagged in the IPFS datastore with the smart contract's blockchain address. When a request is made to retrieve a block from the datastore, if it is tagged, then the security procedures of authentication and authorisation need to occur. 

Authorisation occurs by a remote call from IPFS to the blockchain to query the smart contract specified by the tagged block. The smart contract verifies whether the requestor is authorised to access the IPFS block. If authorised, the IPFS sends the block to the requestor via the IPFS bitswap protocol. 

<p align="center">
<img src="/images/permissioned-block-request.png">
<br>
<b>IPFS Bitswap Authorisation Protocol</b> - A request is made from Bob's IPFS instance to Alice's IPFS instance for a Permissioned Block. The request contains a signed token that authenticates Bob's identity. Alice's IPFS instance makes a remote call to her blockchain instance to verify if Bob is authorised to access the requested block.
</p>

If the requestor successfully receives the IPFS block, it is likewise tagged in the datastore as a Permissioned Block and the same authorisation logic occurs when other node peers request this block.

If the requestor is not authorised, then request is simply ignored. The IPFS DHT router system will need to look elsewhere by querying other  nodes if they have the block. The routing system will continue to search for the requested block until a timeout occurs on the requestor's node. When the timeout occurs, this will signal to the requestor that the block cannot be resolved either because the block does not exist or they do not have permission to access the block.

## A Secure Transmission Channel

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

## Conclusion
In this paper we have introduced a new technology called Permissioned Blocks that is a combination of blockchain and peer-to-peer file sharing technology. Permissioned Blocks allows businesses and individuals to conduct transactions on public blockchains whilst keeping sensitive information private. 

Current blockchain technology is designed to share all transaction data with all nodes in the blockchain network. Permission Blocks changes this by only sharing confidential and personal information with other nodes in the network upon permission being granted.

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

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

If we separate the storage of the smart contract's state from its functional behaviour, then we can create a security model for keeping state information private and reduce the cost of storing data on the blockchain. 

To do this we need to program the functions of a smart contract to be [Pure Functions](https://en.wikipedia.org/wiki/Pure_function). Pure functions by design, do not have the side-effect of storing the state information during the execution of a function.

To employ a pure functional programming model with smart contracts, the previous contract astate is combined with the current input parameters of a function to produce the new contract state.

This new contract state is stored in a linked list structure called a <b>State Chain</b> on a decentralised storage system (a modified version of IPFS), and the hash address of the new state is sent as a transaction message to the network.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>A Smart Contract with Pure Functions</b> - An initial event, with parameter P1 is combined with the initial contract state of S0 to produce the new contract state S1. When another event occurs with the input parameter P2, this produces the new state S2.
</p>

## Privacy of a Smart Contract's State Chain

The privacy of the contract state is controlled using a security model that has access control capabilities. These capabilities determine who can access and decrypt the state information and what contract functions they can execute.   

![permissioned-blocks-capabilities](/images/permissioned-blocks-capabilities.png)

A naive approach would be simply to encrypt the contract state and publish it on a public decentralised network. This approach is vulnerable the information being decrypted by brute force. For the same reason why we use Firewalls in computer networks today, a better security model is to protect the information with a security model that has access control capabilities. Only those that have been granted a certain capability can access some or all of the contract data. 


## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions with internal state.

<p align="center">
<img src="/images/smart-contract-model.png">
<br>
<b>Smart Contract Model</b>
</p>

Each smart contract function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending the input parameters of a function as a transaction message to the network. The transaction is validated by network through a consensus mechanism, Proof of Work in the case of Ethereum, and upon network consensus the new state becomes a permanent part of the blockchain.

The issues with the above behaviour for storing the smart contract state directly on the blockchain are:

- <b> No privacy.</b> The contract's functional state stored on the blockchain's merkel tree and the input parameters sent in the transaction are in clear view for all to see.
- <b> Cost. </b> There is a limited amount of data that can be sent and stored due to the gas price for bytes sent and processed.

## Separating Contract State Mutations from Functional Behaviour

If we store the state information of a smart contract on a separate decentralised storage system and only record the hash of the state mutations then we can create a security model to protect the state information and also reduce the cost of storing the data on the blockchain.

To separate the storage of state from the functional behaviour of a smart contract we need to program each function on a smart contract to be [Pure Functions](https://en.wikipedia.org/wiki/Pure_function). Pure functions by design, do not have the side-effect of storing the state information of a function.

To use a pure functional programming model with smart contracts, the previous contract astate is combined with the current input parameters of a function to produce the new contract state.

This new contract state is stored in a linked list structure called a <b>State Chain</b> on a decentralised storage system (a modified version of IPFS)

The IPFS hash address of the new state is sent as a transaction message to the network.

<p align="center">
<img src="/images/smart-contract-using-pure-functions.png">
<br>
<b>Smart Contract with Pure Functions</b> - The event of parameter P1 and the initial contract state of S0 are combined to produce the new contract state S1. When a new event occurs with the input parameter P2, this produces the new state S2.
</p>

## Privacy of State 

The privacy of contract state is achieved through the using a system of Access Control Capabilities.   

![permissioned-blocks-capabilities](/images/permissioned-blocks-capabilities.png)


## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions with internal state.

<p align="center">
![SmartContractModel](/images/smart-contract-model.png)

Smart Contract Model
</p>


Each smart contract function is programmed to produced a determined output for a set of given input parameters.
State information of a smart contract is modified by sending the input parameters of smart contract function as a transaction message to the network. The transaction is
validated by network through a consensus mechanism, Proof of Work in the case of Ethereum, and upon network consensus the new state becomes a permanent
part of the blockchain.

## Separating Contract State from Functions
  
The issues with the above behaviour of storing the smart contract state information directly on the blockchain are:

- <b> No privacy.</b> The contract's functional state stored on the blockchain's merkel tree and the input parameters sent in the transaction are in clear view for all to see.
- <b> Cost. </b> There is a limited amount of data that can be sent and stored due to the gas price for bytes sent and processed.

If we program a smart contact functions to be [Pure Functions](https://en.wikipedia.org/wiki/Pure_function), then
we could mitigate these issues concerning altogether. Pure functions are a programming paradigm for separating the state of a function from its behaviour. Thus smart contract pure functions do not have the side-effect of mutating the contract state.

To achieve this new model, each state transition is stored on a decentralised storage system (such as IPFS) in a linked list called a <b>State Chain</b>.
When a function is called, the input parameters are combined with the previous state to produce the new contract state.

To record that the state transition has occurred on the blockchain, the IPFS hash address of the new state is sent as a transaction message to the network.

![PureFunctions](/images/smart-contract-using-pure-functions.png)


## Privacy of State 

The privacy of contract state is achieved through the using a system of Access Control Capabilities.   

![permissioned-blocks-capabilities](/images/permissioned-blocks-capabilities.png)


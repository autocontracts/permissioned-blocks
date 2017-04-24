## Smart Contract Model

A simplified model of a smart contract can be modelled as having a set of functions and internal state.

![SmartContractModel](/images/smart-contract-model.png)

A smart contract's function is programmed to produced a determined output for a set of given input parameters.
The state of a smart contract is modified by sending the input parameters to functions as a transaction. The transaction is
validated by network through a consensus mechanism, Proof of Work in the case of Ethereum, and if successful the new state becomes
part of the blockchain.

## Separating Contract State from Functions
  
The issues with the current behaviour of storing the smart contract state directly on the blockchain are:

- No privacy. The contract state and the input parameters sent in the transaction are in clear view for all to see.
- Cost. There is a limited amount of data that can be sent and stored due to the gas price for bytes sent and processed.

If we program a smart contact to have Pure Functions that have no side-effect mutating the contract state then
we could mitigate these issues altogether. Pure functions is a programming paradigm for separating the state of a function from its behaviour.

To achieve this new model, each state transition is stored on a decentralised storage system (such as IPFS) in a linked list called a State Chain.
When a function is called, the input parameters are combined with the previous state to produce the new contract state.

To record the new state transition on the blockchain, only the hash address of the new state is sent in a transaction to the network.

![PureFunctions](/images/smart-contract-using-pure-functions.png)


## Privacy of State 

The privacy of contract state is achieved through the using a system of Access Control Capabilities.   

![permissioned-blocks-capabilities](/images/permissioned-blocks-capabilities.png)


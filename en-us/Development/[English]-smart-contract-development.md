
This guide introduces the development process of smart contracts and issues should be noticed during development process from the perspective of the developer. It guides developers to quickly develop high-quality smart contracts on the PlatON network.

### Smart Contract

A smart contract is a computer protocol designed to propagate, verify, or execute contracts in an informational manner. Smart contracts allow trusted transactions to be made without third parties, which are traceable and irreversible.

The smart contract contains all the information related to the transaction, and the result operation will be performed only after the requirements are met. The difference between smart contracts and traditional paper contracts is that smart contracts are computer-generated. The code itself therefore explains the relevant obligations of the parties.

In fact, participants in smart contracts are usually strangers on the Internet, subject to binding digital protocols. Essentially, a smart contract is a digital contract that will not produce results unless the requirements are met.

### Solidity language

Solidity language is a contract-oriented high-level programming language created to implement smart contracts. Its syntax is similar to JavaScript's high-level programming language. It is designed to generate virtual machine code in a compiled manner. Using it is easy to create smart contracts. However, as a decentralized smart contract running on the Internet in real sense, it has the following features:

* The PlatON is based on an account model,so solidity provides a special address type, which is used to locate user accounts, locate smart contracts, and locate smart contract codes.

* Because solidity embedded framework supports payment, and provides some keywords, such as payable, it can directly support payment at the Solidity language level, which is very simple to use.

* Data storage uses the blockchain on the network, and every state of the data can be stored permanently, so when developing the solidity contract, it is necessary to determine whether the variable uses memory or the blockchain.

* The solidity operating environment is on a decentralized network, with special emphasis on the way ethereum smart contracts or function execution is called. because a simple function call turned into a node code execution on the network, it is a completely distributed programming environment.

* The abnormality mechanism of the solidity language is also very different. Once an exception occurs, all executions will be retracted. This is mainly to ensure the atomicity of smart contract execution to avoid data inconsistencies in the intermediate state.

### Decentralized Applications

Decentralized application (DApp) is an application software that runs on a decentralized point-to-point network. Similar to current mobile applications, DApps are also a type of App. But it does not run on the IOS and Android platforms, but on the operating system of the blockchain network.

DApps are combined with the blockchain through smart contracts. Common DApps are composed of front-end, back-end, and smart contracts. The back-end of DApps can also be implemented through smart contracts. DAPP can connect with PlatON network through PlatON SDK, and SDK can also interact with smart contracts.


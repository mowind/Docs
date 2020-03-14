## Introduce

This tutorial is mainly to guide users to create a simple HelloWorld smart contract using wasm language on PlatON, compile, deploy, and call this contract through platinum-truffle.If you want to use a richer API. 

## Platon-truffle Introduce 

Platon-truffle is a tool provided by PlatON that can compile, deploy, and invoke smart contracts locally. For specific installation and usage manuals, refer to:

- Platon-truffle develop tools[specific installation](https://github.com/PlatONnetwork/platon-truffle/tree/feature/wasm)
- Platon-truffle develop tools[usage manuals](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)


## Create HelloWorld Contract

```c++
#include <platon/platon.hpp>
#include <string>
using namespace platon;

class message {
   public:
      std::string head;
      PLATON_SERIALIZE(message, (head))
};

class my_message : public message {
   public:
      std::string body;
      std::string end;
      PLATON_SERIALIZE_DERIVED(my_message, message, (body)(end))
};

CONTRACT HelloWorld : public platon::Contract{
   public:
      ACTION void init(const my_message &one_message){
        info.self().push_back(one_message);
      }

      ACTION void add_message(const my_message &one_message){
          info.self().push_back(one_message);
      }

      CONST uint8_t get_message_size(){
          return info.self().size();
      }

      CONST std::string get_message_body(const uint8_t index){
          return info.self()[index].body;
      }

   private:
      platon::StorageType<"myvector"_n, std::vector<my_message>> info;
};

PLATON_DISPATCH(HelloWorld, (init)(add_message)(get_message_size)(get_message_body))

```

Contract Files Description:
- Each contract file has only one contract class. The contract class is decorated with Contract. It must be publicly inherited from platon :: Contract and must have an init function.

- ACTION and CONST qualified member functions represent callable functions, and such member functions cannot be overloaded. The ACTION function will modify the data on the chain. The CONST function just queries the attributes and does not modify the data on the chain.

- The type in the callable function parameter list is a custom type. In this type definition, you need to add the PLATON_SERIALIZE macro to declare the serialization function. This type inherits from other types. You need to add the PLATON_SERIALIZE_DERIVED macro to declare the serialization function.

- Callable functions can only be called externally if the unified entry function is defined in the PLATON_DISPATCH macro.

- At present, platon will persistently store member variables of the contract class. The member variables must be of type platon :: StorageType. The first parameter string of the platon :: StorageType template is followed by _n, and the string must be .12345abcdefghijklmnopqrstuvwxyz. 32 characters Characters. The second parameter is the concrete type of the actual storage. Member function modification member variables need to obtain an instance of a specific type through the self () function, and then execute the corresponding instance function.

- The second parameter type of the platon :: StorageType template is a custom type. A PLATON_SERIALIZE macro must be added to this type definition to declare a serialization function. This type inherits from other types. A PLATON_SERIALIZE_DERIVED macro must be added to declare a serialization function.

  


## Compile HelloWorld Contract 

**Step1.** Creat new directory for HelloWorld project 

```
mkdir HelloWorld && cd HelloWorld
```
- The following commands are performed in the HelloWorld directory without special instructions

**Step2.**  Init project

```
truffle init
```
After the command is executed, project directory structure is as follows:

- `contracts/` wasm contract directory

- `migrations/` depoly file directory

- `test/` test script directory

- `truffle-config.js` platon-truffle config

**Step3.** Move HelloWorld contract compiled in to HelloWorld/contracts/

```
ls contracts/
```
- HelloWorld.cpp

**Step4.** Modify the platon-truffle configuration file truffle-config.js and add the compiled wasm contract version number

```
vim truffle-config.js
```

Truffle-config.js content is  as follows:
```
compilers: {
     wasm: {
           version: "1.0.0"
     }
}
```

**Step5.**  Compile contract

```
truffle compile
```
After the command is executed, project directory structure is as follows:

- `build/` wasm contract directory after compiled
- `build/contracts/HelloWorld.abi.json`  HelloWorld contract compiled abi interface file
- `build/contracts/HelloWorld.wasm`  HelloWorld contract compiled binary

## Deploly HelloWorld Contract

**Step1.** Setting config  information for blockchain in truffle-config.js

```
vim truffle-config.js
```
Set blockchain network  info
```
networks: {
	development: {
       host: "10.1.1.6",     // blockchain server address
       port: 8806,            // server port
       network_id: "*",       // Any network (default: none)
       from: "0x5b37dabedae06edb142257819fad207199986992",
       gas: 90000000,
       gasPrice: 50000000004,
	},
}
```

**Step2.** Deploy contract

```
truffle deploy --wasm --contract-name HelloWorld --params '[[["1"], "2", "3"]]'
```
- `HelloWorld` deployed contract
- `params` parameters of contract init function

If deploy success，you wil see log info as follows:
```
receipt:  { blockHash:
   '0x266733b693ba650315a59c34e72804c06ca3e27fab145625797bd42259b572c5',
  blockNumber: 70441,
  contractAddress: '0x0bf45390B486890486e6eB3F1D5C8e0840FD8B56',
  cumulativeGasUsed: 291314,
  from: '0x5b37dabedae06edb142257819fad207199986992',
  gasUsed: 291314,
  logs: [],
  logsBloom:
   '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: null,
  transactionHash:
   '0x60946ebf0ccddc76a0234353435de73e7901888227fb2f03922fb0ce265a4e9d',
  transactionIndex: 0 }
  contract HelloWorld deployed successfully
======================

   > transactionHash:     0x60946ebf0ccddc76a0234353435de73e7901888227fb2f03922fb0ce265a4e9d
   > contract address:    0x0bf45390B486890486e6eB3F1D5C8e0840FD8B56
   > block number:        70441
   > block timestamp:     1583247148341
   > account:             0x5b37dabedae06edb142257819fad207199986992
   > balance:             3533694129556768659166595001485837031654967793751237866225582808584074296
   > gas limit:           100000000
   > gas used:            291314
   > gas price:           0.000000050000000004 LAT
   > total cost:          0.014565700001165256 LAT
```

## Call HelloWorld Contract

**Step1.**  Enter the platon-truffle console

```
truffle console
```
- You can execute command in platon-truffle console

**Step2.**  Create contract object

```json
var abi = [{"baseclass":[],"fields":[{"name":"head","type":"string"}],"name":"message","type":"struct"},{"baseclass":["message"],"fields":[{"name":"body","type":"string"},{"name":"end","type":"string"}],"name":"my_message","type":"struct"},{"constant":false,"input":[{"name":"one_message","type":"my_message"}],"name":"init","output":"void","type":"Action"},{"constant":false,"input":[{"name":"one_message","type":"my_message"}],"name":"add_message","output":"void","type":"Action"},{"constant":true,"input":[],"name":"get_message_size","output":"uint8","type":"Action"},{"constant":true,"input":[{"name":"index","type":"uint8"}],"name":"get_message_body","output":"string","type":"Action"}];
var contractAddr = '0x0bf45390B486890486e6eB3F1D5C8e0840FD8B56';
 
var helloworld = new web3.platon.Contract(abi,contractAddr,{vmType: 1 }); 
```

Description： 
- `abi`  the interface provided by the contract to external calls，the abi  in the file compiled ：`HelloWorld/build/contracts/HelloWorld.json` 
- `contractAddr` contract address
- `helloWorld` contract object created

**Step3.**  Call contract

```javascript
helloworld.methods.add_message('[[["5"], "6", "7"]]').send({
	from: '0x5b37dabedae06edb142257819fad207199986992',gas: 90000000
}).on('receipt', function(receipt) {
	console.log(receipt);
}).on('error', console.error);
```

Description：
- `helloWorld` the contract object created
- `methods` specify the call method
- `add_message`  method in the HelloWorld contract with a custom my_message input
- `from` caller's contract address 
- `on` listen on the result of the contract method executed. if fail, it will print the error info. If success ,the console will print the receipt as belows:

```
{ blockHash:
   '0x669c7b8cb938cc30845e08dc4ceda08f2e17207c267ade97dc5458b445405736',
  blockNumber: 74665,
  contractAddress: null,
  cumulativeGasUsed: 108549,
  from: '0x5b37dabedae06edb142257819fad207199986992',
  gasUsed: 108549,
  logsBloom:
   '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: '0x0bf45390b486890486e6eb3f1d5c8e0840fd8b56',
  transactionHash:
   '0x2b5e590df7e70ad428b1ccb06bda5dcce47f84c4d981c2fb475aca9ec9d0000a',
  transactionIndex: 0,
  events: {} }
{ blockHash:
   '0x669c7b8cb938cc30845e08dc4ceda08f2e17207c267ade97dc5458b445405736',
  blockNumber: 74665,
  contractAddress: null,
  cumulativeGasUsed: 108549,
  from: '0x5b37dabedae06edb142257819fad207199986992',
  gasUsed: 108549,
  logsBloom:
   '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: '0x0bf45390b486890486e6eb3f1d5c8e0840fd8b56',
  transactionHash:
   '0x2b5e590df7e70ad428b1ccb06bda5dcce47f84c4d981c2fb475aca9ec9d0000a',
  transactionIndex: 0,
  events: {} }
```

**Step4.**  Query contract

```javascript
helloworld.methods.get_message_body(0).call()
```
Description：

- `helloWorld` the contract object created
- `methods` specify the call method
- `get_message_body` method in the HelloWorld contract, which has an input parameter of type int
- `call` specify the query method

## FAQ 

> Q. How many commands in platon-truffle？

> Refer to  platon-truffle develop guide[Reference here](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)



> Q. Why the contract can not deploy by truffle migrate?

> Confirm that the configuration information of the chain connected in truffle-config.js and the user's wallet address are correct, and the wallet is unlocked




> Q. platon-truffle failed to deploy constructor contract with parameters

> If the init function in the contract has parameters, you need to specify the params parameter when deploying the contract

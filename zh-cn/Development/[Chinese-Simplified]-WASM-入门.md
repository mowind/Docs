## 简介

WebAssembly（简称 Wasm）是一种为栈式虚拟机设计的二进制指令集。Wasm 被设计为可供类似C/C++/Rust等高级语言的平台编译目标，最初设计目的是解决 JavaScript 的性能问题。Wasm 是由 W3C 牵头正在推进的 Web 标准，并得到了谷歌、微软和 Mozilla 等浏览器厂商的支持。

本教程主要是指导用户在PlatON上使用wasm语言创建简单的HelloWorld智能合约，通过platon-truffle编译，部署，调用此合约。


## platon-truffle开发工具介绍

platon-truffle是PlatON提供的一款能够在本地编译、部署、调用智能合约的工具，具体的安装及使用手册参见

- platon-truffle开发工具[安装参考](https://github.com/PlatONnetwork/platon-truffle/tree/feature/wasm)
- platon-truffle开发工具[使用手册](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)


## 创建HelloWorld合约

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

合约文件说明
- 每一个合约文件只有一个合约类，合约类用 CONTRACT 修饰, 必须公有继承 platon::Contract，必须要有 init 函数。

- ACTION 和 CONST 修饰的成员函数表示可调用函数，此类成员函数不可以重载。ACTION 函数会修改链上数据，CONST 函数只是查询属性不会修改链上数据。

- 可调用函数参数列表中的类型为自定义类型，此类型定义中需加上 PLATON_SERIALIZE 宏声明序列化函数，此类型继承自其他类型，需加上 PLATON_SERIALIZE_DERIVED 宏声明序列化函数。

- 可调用函数只有在PLATON_DISPATCH 宏定义统一入口函数，才能够被外部调用。

- 目前 platon 会将合约类的成员变量持久化存储，成员变量必须是 platon::StorageType 类型，platon::StorageType模板的第一个参数字符串后面加上_n，字符串必须为.12345abcdefghijklmnopqrstuvwxyz这32字符中的字符。第二个参数为实际存储的具体类型。成员函数修改成员变量需要通过 self() 函数获取具体类型的实例，然后执行相应的实例函数。

- platon::StorageType 模板的第二个参数类型为自定义类型，此类型定义中需加上 PLATON_SERIALIZE 宏声明序列化函数，此类型继承自其他类型，需加上 PLATON_SERIALIZE_DERIVED 宏声明序列化函数。

  


## 编译HelloWorld合约 

**step1.** 为HelloWorld项目创建新目录

```
mkdir HelloWorld && cd HelloWorld
```
- 以下命令如果没有特殊说明都在HelloWorld目录下进行

**step2.** 使用platon-truffle初始化一个工程

```
truffle init
```
在操作完成之后，就有如下项目结构：

- contracts/: wasm合约目录

- migrations/: 部署脚本文件目录

- test/: 测试脚本目录

- truffle-config.js: platon-truffle 配置文件

**step3.** 将之前编写好的HelloWorld合约放至HelloWorld/contracts/目录下
```
ls contracts/
```
- 将看到HelloWorld.cpp

**step4.** 修改platon-truffle 配置文件truffle-config.js，添加编译wasm合约版本号

```
vim truffle-config.js
```

truffle-config.js 修改部分内容如下：
```
compilers: {
     wasm: {
           version: "1.0.0"
     }
}
```

**step5.** 编译合约

```
truffle compile
```
在操作完成之后，生成如下目录结构：

- `build/` wasm合约编译后的目录
- `build/contracts/HelloWorld.abi.json`  HelloWorld合约编译后的abi接口文件
- `build/contracts/HelloWorld.wasm`  HelloWorld合约编译后的二进制文件

## 部署HelloWorld合约

**step1.** 修改truffle-config.js中链的配制信息

```
vim truffle-config.js
```
将truffle-config.js中的区块链相关配制修改成您真实连接的链配制
```
networks: {
	development: {
       host: "10.1.1.6",     // 区块链所在服务器主机
       port: 8806,            // 链端口号
       network_id: "*",       // Any network (default: none)
       from: "0x5b37dabedae06edb142257819fad207199986992",
       gas: 90000000,
       gasPrice: 50000000004,
	},
}
```

**step2.** 部署HelloWorld合约

```
truffle deploy --wasm --contract-name HelloWorld --params '[[["1"], "2", "3"]]'
```
- `HelloWorld` :要部署的合约
- `params` :合约init函数对应的参数 

部署成功后，将看到类似如下信息：
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

## 调用HelloWorld合约

**step1.**  进入platon-truffle控制台

```
truffle console
```
- 以下调用查询将在platon-truffle控制台中进行

**step2.**  构建合约对象

```json
var abi = [{"baseclass":[],"fields":[{"name":"head","type":"string"}],"name":"message","type":"struct"},{"baseclass":["message"],"fields":[{"name":"body","type":"string"},{"name":"end","type":"string"}],"name":"my_message","type":"struct"},{"constant":false,"input":[{"name":"one_message","type":"my_message"}],"name":"init","output":"void","type":"Action"},{"constant":false,"input":[{"name":"one_message","type":"my_message"}],"name":"add_message","output":"void","type":"Action"},{"constant":true,"input":[],"name":"get_message_size","output":"uint8","type":"Action"},{"constant":true,"input":[{"name":"index","type":"uint8"}],"name":"get_message_body","output":"string","type":"Action"}];
var contractAddr = '0x0bf45390B486890486e6eB3F1D5C8e0840FD8B56';
 
var helloworld = new web3.platon.Contract(abi,contractAddr,{vmType: 1 }); 
```

说明： 
- `abi` 是合约提供给外部调用时的接口，每个合约对应的abi在编译后的文件中，如：`HelloWorld/build/contracts/HelloWorld.json` 中可以找到
- `contractAddr` 在部署合约成功后有一个contract address
- `helloWorld` 就是构建出来与链上合约交互的合约对象抽象

**step3.**  调用合约

```javascript
helloworld.methods.add_message([["5"], "6", "7"]).send({
	from: '0x5b37dabedae06edb142257819fad207199986992',gas: 90000000
}).on('receipt', function(receipt) {
	console.log(receipt);
}).on('error', console.error);
```

调用合约命令说明：
- `helloWorld` 是之前构建的合约对象
- `methods` 固定语法,指量后面紧跟合约的方法名
- `add_message` 是我们HelloWorld合约中的一个方法，有一个自定义my_message类型的入参
- `from` 调用者的合约地址 
- `gas` gas值
- `on` 是监听合约处理结果事件，此处如果成功将打印回执，失败输出错误日志

函数调用成功，将会看到如下信息：

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

**step4.**  合约查询

```javascript
helloworld.methods.get_message_body(0).call()
```
查询合约命令说明：

- `helloWorld` 是之前构建的合约对象
- `methods` 指定将获取合约中的方法
- `get_message_body` 是我们HelloWorld合约中的一个方法，该方法有一个int类型的入参
- `call` 指明是查询方法

## FAQ 

> 问: platon-truffle有哪些命令如何使用？

> 答: platon-truffle开发使用手册[参考这里](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)



> 问: platon-truffle执行truffle deploy部署合约失败

> 答: 确认truffle-config.js中连接的链的配制信息及用户的钱包地址是否正确,钱包是否解锁




> 问: truffle 部署带参数的构造函数合约失败

> 答: 如果合约中的init函数带有参数，部署合约时需要指定params参数





```

```
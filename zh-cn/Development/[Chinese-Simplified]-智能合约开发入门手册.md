## 简介

本教程主要是指导用户在PlatON上使用solidity语言创建简单的HelloWorld智能合约，通过platon-truffle编译，部署，调用此合约。如果您想使用更加丰富的API可以参考[Java SDK开发指南](/zh-cn/Development/[Chinese-Simplified]-Java-SDK.md) 或者 [JS SDK开发指南](/zh-cn/Development/[Chinese-Simplified]-JS-SDK.md)

- solidity智能合约语法请参考[Solidity官方文档](https://solidity.readthedocs.io/en/develop/)
- 在开发合约前，如果需要搭建节点连接到PlatON网络或者创建私有网络请参考：[连接 PlatON 网络](/zh-cn/Network/)

## platon-truffle开发工具介绍

platon-truffle是PlatON提供的一款能够在本地编译、部署、调用智能合约的工具，具体的安装及使用手册参见

- platon-truffle开发工具[安装参考](https://github.com/PlatONnetwork/platon-truffle/tree/feature/wasm)
- platon-truffle开发工具[使用手册](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)


## 创建HelloWorld合约

```
pragma solidity ^0.5.13;

contract HelloWorld {
    
    string name;
    
    function setName(string memory _name) public returns(string memory){
        name = _name;
        return name;
    }
    
    function getName() public view returns(string memory){
        return name;
    }
}
```

合约文件说明

- pragma solidity ^0.5.13
  -	pragma solidity：是solidity版本声明
  -	0.5.13：代表solidity版本
  -	^ ：表示向上兼容,即可以用0.5.13以上版本编译器进行编译
- contract HelloWorld
  -	contract：合约声明的关键字
  -	HelloWorld：当前合约的名称
- string name
  -	name：合约的状态变量
  -	string：指明此状态变量的类型
- function setName(string memory _name) public returns(string memory)
  -	function：合约中函数声明关键字
  -	setName：此函数的名称
  -	memory：声明_name参数的存储位置（字符串类型的函数输入参数与输出参数必须声明为memory）
  -	_name：为此函数的局部变量
  -	public：声明此函数的可见性
  -	name = _name：此操作将外部传进来的局部变量赋值给状态变量
- function getName() public view returns(string memory)
  -	view:如果一个函数带有view关键字，此函数将不会改变合约中状态变量的值（主要用于查询）		


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

- contracts/: Solidity合约目录

- migrations/: 部署脚本文件目录

- test/: 测试脚本目录

- truffle-config.js: platon-truffle 配置文件

**step3.** 将之前编写好的HelloWorld合约放至HelloWorld/contracts/目录下
```
ls contracts/
```
- 将看到HelloWorld.sol 

**step4.** 修改platon-truffle 配置文件truffle-config.js，将编译器版本修改对应的solidity合约中的版本号

```
vim truffle-config.js
```

truffle-config.js 修改部分内容如下：
```
compilers: {
      solc: {
            version: "^0.5.13",    // 此版本号与HelloWorld.sol中声明的版本号保持一致
      }
}
```

**step5.** 编译合约

```
truffle compile
```
在操作完成之后，生成如下目录结构：

- build/: Solidity合约编译后的目录

- build/contracts/HelloWorld.json  HelloWorld.sol对应的编译文件

## 部署HelloWorld合约

**step1.** 新增合约部署脚本文件

```
cd migrations/ && touch 2_initial_helloword.js
```
部署脚本文件名建议使用合约名称便于后面维护,如HelloWorld合约对应的部署脚本文件为2_initial_helloword.js，内容如下所示：
```
const helloWorld = artifacts.require("HelloWorld"); //artifacts.require告诉platon-truffle需要部署哪个合约，HelloWorld即之前写的合约类名
	module.exports = function(deployer) {
       deployer.deploy(helloWorld); //helloWorld即之前定义的合约抽象
};
```

**step2.** 修改truffle-config.js中链的配制信息

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
       from: "0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e", //部署合约账号的钱包地址
       gas: 90000000,
       gasPrice: 50000000004,
	},
}
```

**step3.**  部署合约

```
truffle migrate
```

部署成功后，将看到类似如下信息：
```
2_initial_helloword.js
Deploying 'HelloWorld'
transaction hash:    0x2bb5c7f6202225554a823db410fb16cf0c8328a51391f24fb9052a6a8f3033e3 //部署合约对应的交易hash
Blocks: 0            Seconds: 0
contract address:    0x714E74eEc4b63D9DB72cbB5F78CDD5b5bb60F9dc  //合约地址（后面合约部署将会使用到）
block number:        142522  //交易对应的块号
block timestamp:     1581667696206
account:             0xF644CfC3b0Dc588116D6621211a82C1Ef9c62E9e //部署合约所使用的账号
balance:             90000000.867724449997417956  //部署合约账户对应的余额
gas used:            149247 //本次部署gas消息
gas price:           50.000000004 gVON
value sent:          0 LAT
total cost:          0.007462350000596988 LAT
Saving migration to chain.
Saving artifacts
Total cost:     0.007462350000596988 LAT
```

## 调用HelloWorld合约

**step1.**  进入platon-truffle控制台

```
truffle console
```
- 以下调用查询将在truffle控制台中进行

**step2.**  构建合约对象

```json
var abi = [{"constant":false,"inputs":[{"internalType":"string","name":"_name","type":"string"}],"name":"setName","outputs":[{"internalType":"string","name":"","type":"string"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"getName","outputs":[{"internalType":"string","name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"}]; //可以从HelloWorld/build/contracts/HelloWorld.json文件中获取到

var contractAddr = '0x9A5015F9A3728ff64f401b9B93E98078BdD48FD1';//部署合约时的获取的地址
var helloWorld = new web3.eth.Contract(abi,contractAddr); 
```

说明： 
- `abi` 是合约提供给外部调用时的接口，每个合约对应的abi在编译后的文件中，如：`HelloWorld/build/contracts/HelloWorld.json` 中可以找到
- `contractAddr` 在部署合约成功后有一个contract address
- `helloWorld` 就是构建出来与链上合约交互的合约对象抽象

**step3.**  调用合约

```javascript
helloWorld.methods.setName("hello world").send({
	from: '0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e'
 }).on('receipt', function(receipt) {
 	console.log(receipt);
 }).on('error', console.error);

```

调用合约命令说明：
- `helloWorld` 是之前构建的合约对象
- `methods` 固定语法,指量后面紧跟合约的方法名
- `setName` 是我们HelloWorld合约中的一个方法，有一个String类型的入参，此处入参为`hello world`
- `from` 调用者的合约地址 
- `on` 是监听合约处理结果事件，此处如果成功将打印回执，失败输出错误日志

函数调用成功，将会看到如下信息：

```
{ 
  blockHash:'0x3ae287d1e745e30d0d6c95d5220cc7816cda955e7b2f013c6a531ed95028a794', //交易所在的区块hash
  blockNumber: 159726, //交易所在的块号
  contractAddress: null,
  cumulativeGasUsed: 44820,
  from: '0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e', //调用者地址
  gasUsed: 44820, //gas消耗
  logsBloom:
   '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: '0x9a5015f9a3728ff64f401b9b93e98078bdd48fd1', //交易调用的合约地址
  transactionHash:'0xb7a41f72d555d4a2d9f2954fbdc5bbbb4c5ce89c836f8704276419ed416b3866', //交易hash
  transactionIndex: 0,
  events: {} 
}
```

**step4.**  合约查询

```javascript
helloWorld.methods.getName().call(null,function(error,result){console.log("name is:" + result);})  
```
查询合约命令说明：

- `helloWorld` 是之前构建的合约对象
- `methods` 指定将获取合约中的方法
- `getName` 是我们HelloWorld合约中的一个方法，该方法没有入参，故入参为空
- `call` 指明是查询方法
- `function` 是一个回调函数，将处理调用后的结果，此处我们通过console.log打印出执行结果
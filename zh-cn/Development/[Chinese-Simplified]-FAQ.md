## 编译相关

1. platon-truffle有哪些命令如何使用？

   platon-truffle开发使用手册[参考这里](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)。

2. 合约为什么语法校验通不过？

   solidity合约0.4.x版本与0.5.x版本有重大变更，具体语法[参考这里](https://solidity.readthedocs.io/en/develop/)。

3. platon-truffle执行truffle compile 失败?

   1.确认编译的合约文件中的版本号与truffle-config.js中指定的版本号是否一致。
   2.可能语法有误，可以根据命令行提示修复后再进行编译。

4. platon-truffle执行truffle migrate部署合约失败?

  1.确认truffle-config.js中连接的链的配制信息及用户的钱包地址是否正确。

5. truffle migrate部署带参数的构造函数合约失败?

  以合约A.sol为例，在migrations/2_initial_A.js文件中，确认是否添加构造参数信息如：
  A.sol构造函数格式如下：
  constructor(uint256 a, string memory b, string memory c) public {}

  2_initial_A.js文件配制如下：
  
   const A = artifacts.require("A");  
   module.exports = function(deployer) {
           deployer.deploy(ERC200513Token,100,'PLA','PLAT');//需要传入对应构造函数参数
   };   


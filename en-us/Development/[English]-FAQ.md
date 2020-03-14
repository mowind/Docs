## FAQ 

1. How many commands in platon-truffle？

   Refer to  platon-truffle develop guide[Reference here](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html).

2. Why contract syntax cannot verify?

   Solidity 0.4.x has a great different with 0.5.x，detail info refer to [Reference here](https://solidity.readthedocs.io/en/develop/)

3. Why truffle doesn't compile?

   Confirm the contract version same as the version specified in the truffle-config.js.
   Contract syntax be writed in a wrong way.

4. Why the contract can not deploy by truffle migrate?

   Confrim the blockchain network info be configured correctly.
   Confirm the account address be configured correctly.

5. Deploying a contract with a parameter constructor using the command `truffle migrate` failed.

    For example, A.sol 
    ```
    Constructor(uint256 a, string memory b, string memory c) public {}
    ```
    2_initial_A.js configured as follow：
    ```
    const A = artifacts.require("A");  
    module.exports = function(deployer) {
            deployer.deploy(ERC200513Token,100,'PLA','PLAT');//pass the corresponding construction parameters
    };
    ```
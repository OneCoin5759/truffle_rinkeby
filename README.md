# truffle_rinkeby

使用truffle部署solidity智能合约到rinkeby
1、安装mist,创建帐号
2、获取eth,方法有几种，plus.google.com 上面分享一条动态，内容为rinkeby的 address，拿到该动态的地址复制到https://faucet.rinkeby.io/获取eth
3、启动geth:  geth --rinkeby --rpc --rpcapi db,eth,net,web3,personal --rpcport 8545 --unlock 0x3B2249987b5db4821aE2105E6Ec083d5e161A4E0 
   稍等输入帐号密码，完成后，窗口不要关掉（aB123456）
4、使用truffle创建项目，如果没安装truffle,使用命令 npm install -g truffle
5、创建项目
    $ mkdir hello-world
    $ cd hello-world
    $ truffle init
6、执行完后会有一个基本的项目骨架， truffle test 可以测试一下，项目是否创建成功
7、开始写我们自己的合约代码，先删掉些默认的文件 
  rm contracts/ConvertLib.sol contracts/MetaCoin.sol
  rm test/TestMetacoin.sol test/metacoin.js
  rm migrations/2_deploy_contracts.js
8、truffle create contract HelloWorld
9、添加下面代码
pragma solidity ^0.4.4;

contract HelloWorld {
  //Begin: state variables
  address private creator;
  address private lastCaller;
  string private message;
  uint private totalGas;
  //End: state variables

  //Begin: constructor
  function HelloWorld() {
    /*
      We can use the special variable `tx` which gives us information
      about the current transaction.

      `tx.origin` returns the sender of the transaction.
      `tx.gasprice` returns how much we pay for the transaction
    */
    creator = tx.origin;
    totalGas = tx.gasprice;
    message = 'hello world';
  }
  //End: constructor

  //Begin: getters
  function getMessage() constant returns(string) {
    return message;
  }

  function getLastCaller() constant returns(address) {
    return lastCaller;
  }

  function getCreator() constant returns(address) {
    return creator;
  }

  function getTotalGas() constant returns(uint) {
    return totalGas;
  }
  //End: getters

  //Being: setters
  function setMessage(string newMessage) {
    message = newMessage;
    lastCaller = tx.origin;
    totalGas += tx.gasprice;
  }
  //End: setters
}

10、创建migration
truffle create migration deploy_hello_world
文件内容如下
const HelloWorld = artifacts.require('HelloWorld')
module.exports = function(deployer) {
  deployer.deploy(HelloWorld)
}

11、修改truffle.js 添加rinkeby网络
module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    rinkeby: {
      host: "localhost", // Connect to geth on the specified
      port: 8545,
      from: "你的rinkeby地址", // default address to use for any transaction Truffle makes during migrations
      network_id: 4,
      gas: 4612388 // Gas limit used for deploys
    }
  }
};

12、部署到rinkeby
  truffle migrate --network rinkeby
13、交互
 $ let contract
 $ HelloWorld.deployed().then(instance => (contract = instance));
 $ contract.getMessage()/contract.getMessage.call() //调用读方法
 $ contract.setMessage('test',{from:'rinkeby地址'})
 


   

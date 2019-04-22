# Ethereum

账户余额模型，ethereum1.0 抗ASIC的工作量证明，ethereum2.0 POS

## EthereumJ源码解析

TransactionExecutor.call\(\) -&gt;  
        precompiledContract = PrecompiledContracts.getContractForAddress\(     targetAddress, blockchainConfig\);  
        precompiledContract.execute\(tx.getData\(\)\) //预定义的计算 or   
        { this.vm = new VM\(config, vmHook\); this.program = new Program\(\); }

TransactionExecutor.create\(\) -&gt;  
        Repository.createAccount\(\)  




## 框架设计

### 状态

* 用户状态
* 合约状态
* 密钥

### Message / transaction

### blockchian

#### 叔块 GHOST协议

#### 数据结构与编码

* RLP
* TRIE 

#### 内存模型

#### 支付过程与事务验证

#### Mining

* 抗ASIC的工作量证明Ethash
* 难度计算

#### Ethereum协议应用

* Whisper
* Swarm
* Libp2p
* Devp2p

#### 智能合约与EVM 

#### 联盟链开发

#### 分片

#### 权益证明

* Casper FFG 
* Casper CBC 
* Rocket pool：使用Casper的应用




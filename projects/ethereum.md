# Ethereum

账户余额模型，ethereum1.0 抗ASIC的工作量证明，ethereum2.0 POS

## EthereumJ源码解析

TransactionExecutor.call\(\) -&gt;  
        precompiledContract = PrecompiledContracts.getContractForAddress\(     targetAddress, blockchainConfig\);  
        precompiledContract.execute\(tx.getData\(\)\) //预定义的计算 or   
        { this.vm = new VM\(config, vmHook\); this.program = new Program\(\); }

TransactionExecutor.create\(\) -&gt;  
        Repository.createAccount\(\)  




## 账户余额模型




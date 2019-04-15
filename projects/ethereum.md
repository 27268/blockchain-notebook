# Ethereum

## Ethereum理论

## EthereumJ源码解析

TransactionExecutor.call\(\) -&gt;  
        precompiledContract = PrecompiledContracts.getContractForAddress\(     targetAddress, blockchainConfig\);  
        precompiledContract.execute\(tx.getData\(\)\) //预定义的计算 or   
        { this.vm = new VM\(config, vmHook\); this.program = new Program\(\); }

TransactionExecutor.create\(\) -&gt;  
        Repository.createAccount\(\)  





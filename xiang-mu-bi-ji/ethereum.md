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
* TRIE在Ethereum中的应用 
  * state trie: key -&gt; sha3\(address\), value -&gt; rlp\(account info\)
  * transaction trie: key -&gt; rlp\(transaction index\), value -&gt; \(transaction info\)
  * receipts trie: key -&gt; rlp\(transaction index\), value -&gt; \(receipts info\)
  * Storage trie: account中保存有这个storage trie的root，账户数据被分为256-bit的integer，作为value，将value的 keccak hash作为key，

1. 关于Merkle树：在证明一个交易是否存在于一个block中时，如果不用Merkle树，直接对所有交易（假设有n个）进行hash，需要进行n次hash；使用Merkle树之后，只需要对树的每一层进行两次hash即可，2\*\[log2\(n\) + 1\]次。
2. 关于Patricia树：压缩前缀树，用于保存键值对。

```java
    public final class Node {
        //Trie节点的类型：BranchNode, KVNodeNode, KVNodeValue
        public NodeType getType() {
            parse();

            return children.length == 17 ? NodeType.BranchNode :
                    (children[1] instanceof Node ? NodeType.KVNodeNode : NodeType.KVNodeValue);
        }
```



#### 内存模型

#### 支付过程与事务验证

#### Mining

* 抗ASIC的工作量证明：在Ethash生成Dataset和cache，计算hash需要dataset or cache中的数据
* 难度计算：难度根据 生成上一个块所需要的时间 与 期望时间 之间的关系进行调整。

```java
public class EthashAlgo {
    //挖矿：计算nonce
    public long mineLight(long fullSize, final int[] cache, byte[] blockHeaderTruncHash, long difficulty, long startNonce) {
        long nonce = startNonce;
        BigInteger target = valueOf(2).pow(256).divide(valueOf(difficulty));
        while(!Thread.currentThread().isInterrupted()) {
            nonce++;
            Pair<byte[], byte[]> pair = hashimotoLight(fullSize, cache, blockHeaderTruncHash, longToBytes(nonce));
            BigInteger h = new BigInteger(1, pair.getRight() /* ?? */);
            if (h.compareTo(target) < 0) break;
        }
        return nonce;
    }
    public Pair<byte[], byte[]> hashimotoLight(long fullSize, final int[] cache, byte[] blockHeaderTruncHash,
                                               byte[]  nonce) {
        return hashimoto(blockHeaderTruncHash, nonce, fullSize, cache, false);
    }
    public Pair<byte[], byte[]> hashimoto(byte[] blockHeaderTruncHash, byte[] nonce,
                                          long fullSize,
                                          int[] cacheOrDataset, boolean full) {
        if (nonce.length != 8) throw new RuntimeException("nonce.length != 8");

        int hashWords = params.getHASH_BYTES() / 4;
        int w = params.getMIX_BYTES() / params.getWORD_BYTES();
        int mixhashes = params.getMIX_BYTES() / params.getHASH_BYTES();
        int[] s = bytesToInts(HashUtil.sha512(merge(blockHeaderTruncHash, reverse(nonce))), false);
        int[] mix = new int[params.getMIX_BYTES() / 4];
        for (int i = 0; i < mixhashes; i++) {
            arraycopy(s, 0, mix, i * s.length, s.length);
        }

        int numFullPages = (int) (fullSize / params.getMIX_BYTES());
        for (int i = 0; i < params.getACCESSES(); i++) {
            int p = remainderUnsigned(fnv(i ^ s[0], mix[i % w]), numFullPages);
            int[] newData = new int[mix.length];
            int off = p * mixhashes;
            for (int j = 0; j < mixhashes; j++) {
                int itemIdx = off + j;
                if (!full) {
                    int[] lookup1 = calcDatasetItem(cacheOrDataset, itemIdx);
                    arraycopy(lookup1, 0, newData, j * lookup1.length, lookup1.length);
                } else {
                    arraycopy(cacheOrDataset, itemIdx * hashWords, newData, j * hashWords, hashWords);
                }
            }
            for (int i1 = 0; i1 < mix.length; i1++) {
                mix[i1] = fnv(mix[i1], newData[i1]);
            }
        }

        int[] cmix = new int[mix.length / 4];
        for (int i = 0; i < mix.length; i += 4 /* ? */) {
            int fnv1 = fnv(mix[i], mix[i + 1]);
            int fnv2 = fnv(fnv1, mix[i + 2]);
            int fnv3 = fnv(fnv2, mix[i + 3]);
            cmix[i >> 2] = fnv3;
        }

        return Pair.of(intsToBytes(cmix, false), sha3(merge(intsToBytes(s, false), intsToBytes(cmix, false))));
    }

}
//更新difficulty
public class BlockHeader{
    public BigInteger calcDifficulty(BlockchainNetConfig config, BlockHeader parent) {
        return config.getConfigForBlock(getNumber()).
                calcDifficulty(this, parent);
    }
}
/**
 * pd = parentDifficulty 
 * fromParent = pd + pd/2048*max(1-(curBlock.timestamp-parent.timestamp)//10, -99)
 * return max(fromParent, 131072);
 */
public abstract class AbstractConfig{
    @Override
    public BigInteger calcDifficulty(BlockHeader curBlock, BlockHeader parent) {
        BigInteger pd = parent.getDifficultyBI();
        BigInteger quotient = pd.divide(getConstants().getDIFFICULTY_BOUND_DIVISOR()/*2048*/);

        BigInteger sign = getCalcDifficultyMultiplier(curBlock, parent);
//BigInteger.valueOf(Math.max(1 - (curBlock.getTimestamp() - parent.getTimestamp()) / 10, -99));

        BigInteger fromParent = pd.add(quotient.multiply(sign));
        BigInteger difficulty = max(getConstants().getMINIMUM_DIFFICULTY(), fromParent);

        int explosion = getExplosion(curBlock, parent);

        if (explosion >= 0) {
            difficulty = max(getConstants().getMINIMUM_DIFFICULTY(), difficulty.add(BigInteger.ONE.shiftLeft(explosion)));
        }

        return difficulty;
    }
}
```

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




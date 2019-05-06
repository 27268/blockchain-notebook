---
description: >-
  2018 12th International Conference on Open Source Systems and Technologies
  (ICOSST)
---

# A Comparative Analysis of DAG-Based Blockchain Architectures

{% embed url="https://ieeexplore.ieee.org/document/8632193" %}



1. 传统区块链的工作方式
2. 传统区块链的弊端
   1. 扩展性
   2. 交易费
   3. 冗余和成本
   4. 高成本导致的中心化（矿工、全节点）
3. 传统区块链 -&gt; 基于DAG的区块链：交易之间的互相验证
4. 现有的基于DAG的区块链
   1. NXT
   2. IOTA
   3. Orumesh
   4. DAGCoin
   5. Byteball
   6. Nano

![&#x4F20;&#x7EDF;&#x533A;&#x5757;&#x94FE;&#x4E0E;&#x57FA;&#x4E8E;DAG&#x7684;&#x533A;&#x5757;&#x94FE;&#x5BF9;&#x6BD4;](../.gitbook/assets/image%20%2850%29.png)

### NXT

* Proof-of-Stake: 可以对抗Nothing-at-Stake
* DAG: 
* 1 billion token within Genesis account in Genesis block 

#### Forging transactions / block creation

60 s 生成一个block，367200 transactions per day \(4.25 tps\)。10个block之后，认为系统安全。

在NXT中，block reward仅为交易费，因此，不会生成新的token，只是token在用户和矿工之间的重新分配。这也是为什么出块的过程被称为forging，而不是mining。

#### Proof of Stake

持有stake最多的5个人，生产了42%的block。

用户持有的每一个token，都可以被视为1个小型矿机，持有的token越多，矿工出块的概率越大。

* block中保存 累计权重 cumulative weight 参数，分叉时以累计权重最大的block为准
* tokens must be constant within an account for 1,440 blocks before they can participate to the block generation process. 用户持有时间超过1440个block的token才能参与出块过程
* 防止从创世块开始构建block：h 表示 current height，高度小于 h-720 的block将被拒绝。分叉高度不会高于720

### IOTA




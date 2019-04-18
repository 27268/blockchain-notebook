---
description: >-
  2018 International Conference on Computing, Networking and Communications
  (ICNC)
---

# Performance Analysis and Application of Mobile Blockchain

[https://ieeexplore.ieee.org/document/8390265](https://ieeexplore.ieee.org/document/8390265)

要点总结：

创新点：专门针对移动商务；工作量证明，降低难度-用在移动设备上，同时也使得块不能太大（网络延时严重）

存在的问题：难度低，容易分叉，没有分叉应对策略。

## 立题依据：

存在的问题：

移动商务   
 -》移动商务中的安全问题（没具体说明？the key concern about security for m-commerce transactions needs to be addressed to facilitate the continued growth of m-commerce.  
 -》blockchain introduced as an effective technology for data security problem   
        -》区块链简介

贡献：

A new mobile commerce application using blockchain technology - MobiChain.  
-》移动区块链应用程序的api，支持在移动设备上的挖矿  
-》性能评估：计算时间、能耗、内存利用率

相关工作：

* 区块链在各个领域的应用：智能物联网房屋，去中心的个人数据管理平台、
* 新的挖矿方式：改进加密算法、基于reputation的共识、
* 可伸缩性scalability：BigChainDB，
* 移动设备上的挖矿：EasyMiner，LTC，Scrypt，MinerPro

## 系统模型及评估

![MobiChain&#x7CFB;&#x7EDF;&#x6A21;&#x578B;](../.gitbook/assets/image%20%284%29.png)

### 角色

M\(X\) - mobile node, X in {A, B, C, D, E}, S\(Y\) - service node, Y in {F, G, H}

* mobile node：通过internet或本地连接与service node相连，且假定所有的mobile node都安装了MobiChain应用程序，注册了user account，each mobile node has a backlog or queue to store pending tasks.
* service node：安装了用于广播数据的Sync gateway，service node之间互相连接

### 消息传递过程

以M\(A\)向M\(C\)传递消息为例：

1. Sender  create a transaction. transaction consists of text message, sender info, receiver info.
2. Sender upload the transaction to its connected server S\(F\),
3. S\(F\)中的同步网关将transaction广播到网络中的所有在线节点，即M\(B\), M\(C\), M\(D\).
4. M\(E\)重新上线后，首先更新和同步当前区块链和pending tasks.

![mining process](../.gitbook/assets/image%20%287%29.png)

### 挖矿过程

假设M\(B\)和M\(C\)是专用矿工，或者他们想要mine this transaction from A to C：

1. Verify transaction. 矿工从backlog中查询transaction并进行验证，验证内容：1）未被修改，2）在区块链中不存在。若验证结果为true, 进入2；若结果为false，将结果通知给网络中其他节点。
2. Find the latest block. 矿工找到网络中最新的block，获取其ID；若链是空的，记为0。The latest block may be referred to as the previous block.
3. Proof of work. 迭代的hash - sha3\_256，输入为：1\) the previous block's ID, 2\) the block number, 3\) the verified transaction, 4\) the timestamp, 5\) the signature of the miner.
4. Verify all information. 验证内容：1\) hash ID and transaction information in the chain, 2\) the existing blocks in the chain. 验证目的：确保链上的block信息没有被改变过。
5. 检查其他节点是否已经将 the created block添加到链上，若没有，矿工将 the created block 添加到链上（广播出去）。

### 系统核心模块——application programming interface.

* Database function
  * 数据结构 in json：account \(私钥保存在本地，公钥广播给其他节点\),  transaction, block
  * 数据库：嵌入式JSON数据库 Coushbase Lite，适用于实时系统
  * Coushbase Sync Gateway

![account&#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;](../.gitbook/assets/image%20%2819%29.png)

![transaction&#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;](../.gitbook/assets/image%20%2814%29.png)

![block&#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;](../.gitbook/assets/image%20%282%29.png)

* Main function
  * 当节点创建交易时，调用Main Function创建交易，并将该交易保存的当前节点的backlog中
  * 当节点接收到交易时，调用MF 将收到的交易保存到当前节点的backlog中
  * 周期性的检查节点的backlog是否未空，不为空时，the Main function perform the mining process.
* Cryptography function
  *  SHA3\_256
  * 注册account时，ED25519 public key signature system
  * encode and decode process, Base56

### 性能评估

* 内存利用率
  * 不同的block size \(1 tx, 3 tx, 6 tx\)，memory utilization = cb + ct \* T + cd \* D， 其中，cb: size of block information, ct: size of transaction, cd: size of one degit, T: the number of transaction in one block, D: the number of digits in block\_number
* 工作量证明过程
  * 要求：hash值前三位为0，实验设备每秒可执行803次hash。
  * 使用移动设备创建7156个block需要3.5天，88.06%的block需3-30s，4.79%的block需要超过100s
* 链验证过程
  * 验证过程中的能源消耗和执行时间
  * 当block中的交易数量增加时，验证过程的能耗与执行时间都会降低（但会提高网络延时）
  * 验证线程数量从1到2，从2到4时，执行时间降低为原来的一半，继续增加线程数时，效果不明显（Android一般支持8核）




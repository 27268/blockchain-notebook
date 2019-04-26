---
description: 'NSDI 2019; http://monoxide.io'
---

# Monoxide

要点总结：

* 异步共识：共识组之间的消息通信模式（relay transaction）
* 诸葛连弩的挖矿方式：矿工可以同时为多个共识组挖矿

问题：是否存在不能被拆分出relay transaction的交易情景？

## 问题描述：从分布式系统开始

状态机模型：

* UTXO模型——全量模型，功能扩展很难
* account / balance 模型——增量模型

状态机的性能：

* 状态容量：内存中能够维护的状态数量
* 吞吐量：CPU + IO

分布式状态机：所有节点都可以发起状态的更新，并将状态更新同步给其他所有节点。  
 -》发起状态更新——出块，需要计算资源  
 -》同步状态更新——广播区块，IO 需要网络带宽  
区块链根据 已确认交易 更新状态机的状态，通过 block 对交易进行分批验证、确认。

若有多个节点发起状态更新，系统以哪个节点的更新为准？  
 -》共识算法：决定出块节点、出现分歧时如何解决。  
POW：后置共识。在分叉之后选择有效分支——缺点：会分叉  
PBFT：前置共识。解决分歧之前不出块——缺点：网络可能会停。  
POS在实际应用中都结合了PBFT。

补充：POW 解决了拜占庭问题？  
拜占庭问题在问题描述中，确定了是要在N个将军之间达成共识。  
POW不需要确定参与者人数，POW比拜占庭更通用，解决的是完全不同的问题。

区块链系统的性能瓶颈出现在哪里？  
-》区块链中的节点都需要做哪些工作？什么工作限制了区块链性能的提升？

区块链全节点的工作内容与资源需求：

* 广播区块信息和未确认交易——网络带宽
* 验证交易，更新账簿状态——计算
* 保存账簿历史——硬盘
* 维护最新状态——内存

其中，硬盘、计算资源、内存都是市场化的资源，除了网络带宽，一个节点能够获得的网络带宽是受基础设施条件限制的。

 TPS = blocksize / \( TxNumber \* TxSize \* blockInterval \)  
其中，blockInterval 不是随便选择的，要能使 block 在网络之中充分传播。即 block 在网络中充分传播之后才能出下一个 block。  
单个节点的最大吞吐量：\( average internet bandwidth \) / \( average transaction size \)。若超过这个值，block在网络中无法得到充分传播，就容易出现分叉。

![&#x5DE5;&#x4F5C;&#x91CF;&#x8BC1;&#x660E;](../.gitbook/assets/image%20%2820%29.png)

lower hash difficulty  
--&gt; less block interval   
--&gt;  not fully broadcast \(跟不上节奏的全节点\)   
--&gt; higher fork rate 

![POS+PBFT](../.gitbook/assets/image%20%2829%29.png)

lower epoch time --&gt; higher failure rate

去中心化的通信：Internet  
去中心化的存储：Bittorrent、DHT   
去中心化的计算：blockchain、云计算（二者的区别：计算中心是否可信）

区块链不可能三角：

* 安全：fast fully replication
* 性能：状态容量，吞吐量（单机维护整个网络状态）
* 去中心化：全节点的门槛足够低

单机维护整个网络状态的情况，只出现在单链系统中，在多链或者分片系统中，单机维护自己所在链/分片系统即可。  
跨链：以业务为基本粒度对系统进行切分  
分片：可以直接对单个大业务进行切分  
 -》扩容方案：分片。  
分片的难点：

1. 分片带来算力分散，如何保持系统安全性
2. 跨片交易

## 架构设计：Asynchronous Consensus Zones 

分片之后的安全性：Chu-ko-nu mining  
跨片交易：relay transaction

### 系统概述

#### cross-zone 交易过程

以付款交易为例进行说明：Alice in Zone A, Bob in Zone B, Alice转给Bob金额x,  
付款交易包含两个操作：从 Alice 的账户扣去 x，需要判断Alice 的帐户上是否有足够的余额；给Bob的账户加上 x。  
执行过程：  
withdraw operation  
        1. Alice创建 initiate transaction并广播（initiate transaction执行从Alice的账户中扣款的动作）  
        2. 矿工验证交易，若验证通过，将 initiate transaction 打包到 block \(for zone A\) 中  
deposit operation  
        3. \(默认由2中的矿工\)创建relay transaction，并转发到zone B，  
        4. 矿工将relay transaction添加到 block \(for zone B \)中

![&#x8F6C;&#x8D26;&#x8FC7;&#x7A0B;](../.gitbook/assets/image%20%2828%29.png)

在上述过程中，initiate TX 是有条件的 \(Alice.balance &gt; x \)，有顺序的（交易顺序影响交易执行结果）  
Relay TX 是无条件的、无序的。

#### zone partitioning and naming

账户/用户地址 ：a fixed hash value of its public key.  
zone partitioning: partitions the space of user addresses into 2^k zones in a fixed and deterministic way, a zone is identified by its sharding scale k and zone index s \(s in \[0,2^k -1\]\)

![zone partition](../.gitbook/assets/image%20%2841%29.png)

Block is identified with &lt;s, k, h&gt;, s: zone index, k: sharding scale, h: height of chain in the zone.

根据DHT进行路由

全节点仅同步自己所在zone中的 transaction 和 block。（后续会说明矿工需要同步它想要挖矿的zone中的信息）——由于交易的设计，全节点可以仅根据自己所在zone的 transaction 和 block 对新交易进行验证，不需要同步全网的交易历史，从而降低全节点的门槛。

这里矿工的门槛依然很高。

### cross-zone atomicity

* chaining-block for the chain formation and POW verification
* transaction-block carrying actual confirmed transactions

![chaining-block &#x4E0E; transaction-block &#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;](../.gitbook/assets/image%20%2812%29.png)

属性说明：

* Merkle Root of Confirmed TX
* Merkle Root for the list of all relay transactions originated from initiative transactions in this block -&gt; used for validation of relay transactions in other zones.

#### 交易过程

1. Transaction validation and forwarding at zone A.
2. Relay transaction handling at zone B.

#### Verification

* Transaction verification
* Block verification

#### 最终原子性

将原子操作拆分成两个操作，前序操作确认之后，后序操作一定可以执行（无条件、无时序）。

#### 冲突解决

### defense per-zone security

#### 诸葛连弩

![Chu-ko-nu Mining](../.gitbook/assets/image%20%282%29.png)

矿工找到的一个合适的nonce可以在多个zone中获取收益。

### 分区扩展


# 3D-DAG: A high performance DAG network with Eventual consistency and finality

{% embed url="https://ieeexplore.ieee.org/document/8605990" %}

总结要点：

利用关注点分离（SOC）的体系结构解决不可能三角。引入 Software-Defined Chain.

SOC: Separation of Concern.

## 系统设计

### 架构概述

* main blockchain: 挖矿，保证系统安全
* multiple DAG chains: 业务需求在各个DAG上实现，提高性能和吞吐量

主要角色：

* miner: 在主链上挖矿，保证整个系统的安全
  * POW: reward 
  * POV: reward of transaction and world state transition validation
* DAG roles: light node of different DAGs for transaction initiation
  * assert based transactions -&gt; transaction output DAG
  * world state based transactions -&gt; world state transition DAG
* endorser: 执行智能合约，为来自轻节点的复杂状态转换背书；保存每个DAG的状态；
  * receive reward for maintaining DAG's world state

DAG chains 根据 chain template 创建，因此 DAG chain 也被成为 software-defined chain \(SDC\)

SDC 允许用户定义 endorsement and validation policy, and DAG chain parameter.

### 3D-DAG consensus mechanism？

miner可以获得一份POW激励 \(1 3D-dag coin\)，多份POV激励 ——意在鼓励矿工验证交易。

![&#x7CFB;&#x7EDF;&#x67B6;&#x6784;](../.gitbook/assets/image%20%2814%29.png)

假设一个给定的DAG，需要 n consenters, n/2+1 votes for finalized a checkpoint,   
其中，consenters 验证 transaction output in DAG,   
检查点周期 \( checkpoint cycle \) 以主链块高度间隙定义，eg: 每10块 block in main chain 一个检查点，


---
description: IEEE access 2019
---

# DLattice: a permission-less blockchain based on DPOS-BA-DAG consensus for data

{% embed url="https://ieeexplore.ieee.org/document/8672629" %}

## 要点总结

* DPOS - delegate to 持有consensus ID的节点
  * Boot阶段：所有节点都可以参与共识
  * Freedom阶段：计算到符合条件hash的节点可以参与共识
* BA - 超过2/3的节点支持一个分支上的TB
* DAG - 数据组织形式

在发现分叉时才运行共识算法。

存在的问题：检测到分叉之后才运行共识算法，不能用于即时支付，容易受到日蚀攻击。

## 立题依据

实际案例：

1. 酒店客户信息泄露
2. Facebook数据泄露

引出的要解决的问题：

1. The control over the data is difficult to determine.
2. Data reliability is poor and can be falsified.
3. Data cannot be shared efficiently.

文章贡献：

1. A permission-less blockchain - DLattice. Each account has its own account-dag, and all accounts make up a node-dag.
2. A DPOS-BA-DAG protocol. 仅在出现分叉时，实现共识。一般是周期性共识。
3. 基于DLattice的data tokenization: data assenbling, data anchoring, data authorization.

## DLattice

### 名词定义

* consensus-participating node：参与共识的节点，共N个，本质是一段程序。记为CNode。
  * 职责：记录asset ledger and data ledger，通过gossip协议沟通
  * CNode中包含共识账号（consensus account, ConAC）和普通账号（Normal account, NorAC）
* Account：用户参与网络的主体，共M个账户，理论上M没有上限
  * NorAC：currency ledger \(用于收发currency\) + data ledger\(用于收发数据、分配访问控制权\)
  * ConAC：NorAC + Definition 1\(? 推测应该是共识算法\)，需要抵押asset
* Transaction and block: 这里的block仅包含一个交易，因此也叫TransactionBlock，记为TB
  * TBcreate: create account.
  * TBdelegate: assign a consensus node to wield voting power on its behalf.
  * &lt;TBsend, TBreceive \| TBdeal&gt;: transfer of data assets and currency assets.
  * TBauth: determines which account has the access control of the data assets. 
  * STATEtb in {Ssending, Spending, Sreceived}. 
* Digital asset: asset in the form of  electronic data owned by sb and held for sale.
  * currency asset
  * data asset - the result of data tokenization.
* DLattice：Node-DAG consists of account-dag and genesis header.
  * account-dag 以MPT的形式组织在Node-dag中，账户公钥作为key, value= T-chain + D-Tree\(红黑树 保存TBdata\)

![account-dag](../.gitbook/assets/image%20%2828%29.png)

Spending指的是所有的\(共识\)节点都已收到该交易，Sreceived: receiver has received the currency or the data, and consturcted the coresponding TBreceive or TBdeal.

### 假设

1. standard cryptographic assumption
2. 诚实节点无故障，且诚实节点持有超过2/3的token
3. strong synchrony : 诚实节点发出的message可以在一个已知的time bound之内被绝大多数诚实节点收到。
4. weak synchrony: the network may be asynchronous for a long but bounded period of time.
5. if probability p is negligible, p =  $$O(1/2^r)$$ , r 表示 安全系数。high probability = 1 - O\(1/2^r\)

## Data tokenization

### Data Assembling

assembly the raw data into a data structure  
D = \(Pkp, Pko, Ek\(Draw\), Eek, T, Sigsk\(Draw\)\);

* Pkp 数据生产者公钥
* Pko 数据拥有者公钥
* Ek\(Draw\) AES加密数据密文
* Eek 生成Ek\(Draw\)所用的密钥 的非对称加密的密文
* T 数据生成时的时间戳
* Sigsk\(Draw\) 数据生产者对原始数据的签名

### Data Authorization

Data owner授权其他账号other访问数据：需要other的公钥，用于对&lt;key, **iv**&gt;进行非对称加密。other利用&lt;key, iv&gt;解密密文。

![&#x6DF7;&#x5408;&#x52A0;&#x5BC6; &amp;lt;key, iv&amp;gt;](../.gitbook/assets/image%20%281%29.png)

### Data Anchoring

在数据保存到分布式存储中之后，anchor the digital fingerprint of the data assert to the blockchain.

1. TBsend被所有共识节点收到（达成共识）之后，将TBsend加入到T-chain中，
2. receiver收到TBsend之后，创建TBdeal并添加到T-chain，更新D-Tree，完成数据传输。

## DPOS-BA-DAG consensus

### node bootstrapping

1. Boot epoch: 节点数 in \[4, Cb\]，Cb = 200. Genesis node reviews the online and storage capabilities of new node\(医疗机构、政府机构、大型企业等可信节点\)，并为其创建DLTinit to complete the joining of the boot node. 每个节点都知道系统中的节点总数。
2. Freedom epoch: 节点数 &gt; Cb \(200\) 此时，新加入的节点需要从其他账户购买DLT，以创建normal account。

### fork observation

![&#x5206;&#x53C9;&#x7684;&#x5B9A;&#x4E49;](../.gitbook/assets/image%20%286%29.png)

若有节点（记为Candidate-seed ）发现分叉，执行共识算法，确定有效的TB，

![&#x5171;&#x8BC6;&#x7B97;&#x6CD5;&#x7684;&#x6267;&#x884C;&#x8FC7;&#x7A0B;](../.gitbook/assets/image%20%287%29.png)

### consensus indentity setup

Boot epoch：Candidate-seed每轮共识中都需要计算自己的consensus identity：  
                          IDseed = &lt;Pk, Seed, hash, proof, message, Sigsk&gt;

Freedom epoch：Candidate-seed需要计算符合要求的hash，才能计算consensus identity：  
                                  IDseed = &lt;Pk, Seed, IOpos&lt;hash&gt;, Sigsk&gt;  
其中hash &lt;= wi/W，wi为该节点持有/represent的voting power，W为总的voting power；seed与Hpre相关。

### committee formation 形成委员会（in DPOS）

1. ConsensusIDGeneration\(\)
2. VerifyID\(\)

### consensus

![&#x5171;&#x8BC6;&#x7B97;&#x6CD5;](../.gitbook/assets/image%20%284%29.png)

共识阶段包含两个子阶段

* Vote Phase：committee选择一个TB - ForkSelection\(\)，所有的节点统计投票结果 - CountMsg\(\)
* Commit phase ：基于投票结果进行提交 - CommitteeMsg\(\)，支持一个TB的节点数超过tgood\(2/3\)时，达成共识

##  攻击向量与安全性分析

### 攻击向量：

* 双花 - （分叉问题）
  * 每个TB创建时，需要reserve the Fork Penalty
  * 一旦分叉就启动共识算法确定有效TB
* 女巫攻击：在POS机制中，持有节点不会攻击到网络，持有token才能实施攻击，但持有token的成本很高
* DDOS？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？
* fluctuation of nodes - 节点波动

### 安全性分析！！！

## 系统实现与评估

系统实现：参数设置

系统评估：系统延迟、吞吐量、相关系统的比较

## 总结与展望

问题：

1. 原始数据保存在IPFS上，安全性无法保障
2. 不支持智能合约
3. If the consensus is not reached in the first term, the identity of the corresponding consensus committee membership will be exposed and vulnerable to DDOS attacks.

应用展望：tokenization of medical data and IOT data.



说明：图片来自论文


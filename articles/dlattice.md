---
description: IEEE access 2019
---

# DLattice: a permission-less blockchain based on DPOS-BA-DAG consensus for data

{% embed url="https://ieeexplore.ieee.org/document/8672629" %}

要解决的问题：

1. The control over the data is difficult to determine.
2. Data reliability is poor and can be falsified.
3. Data cannot be shared efficiently.

文章贡献：

1. A permission-less blockchain - DLattice. Each account has its own account-dag, and all accounts make up a node-dag.
2. A DPOS-BA-DAG protocol. 仅在出现分叉时，实现共识。一般是周期性共识。
3. 基于DLattice的data tokenization: data assenbling, data anchoring, data authorization.

## DLattice

名词定义

* consensus-participating node：参与共识的节点，共N个，本质是一段程序。记为CNode。
  * 职责：记录asset ledger and data ledger，通过gossip协议沟通
  * CNode中包含共识账号（consensus account, ConAC）和普通账号（Normal account, NorAC）
* Account：用户参与网络的主体，共M个账户，理论上M没有上限
  * NorAC：currency ledger \(用于收发currency\) + data ledger\(用于收发数据、分配访问控制权\)
  * ConAC：NorAC + Definition 1\(? 推测应该是共识算法\)
* Transaction and block: 这里的block仅包含一个交易，因此也叫TransactionBlock，记为TB
  * TBcreate: create account.
  * TBdelegate: assign a consensus node to wield voting power on its behalf.
  * &lt;TBsend, TBreceive \| TBdeal&gt;: transfer of data assets and currency assets.
  * TBauth: determines which account has the access control of the data assets. 
  * STATEtb in {Ssending, Spending, Sreceived}. 
* Digital asset: asset in the form of  electronic data owned by sb and held for sale.
  * currency asset
  * data asset - the result of data tokenization.

Spending指的是所有的\(共识\)节点都已收到该交易，Sreceived: receiver has received the currency or the data, and consturcted the coresponding TBreceive or TBdeal.

## Data tokenization



## DPOS-BA-DAG consensus


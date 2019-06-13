# Bitcoin

UTXO，工作量证明

## Blockchain

### block的数据结构

block大小，block的验证

### 工作量证明

所用hash算法：hashcash，难度计算与更新

### 共识规则的更新

#### 软分叉和硬分叉

#### Bitcoin分叉案例

软分叉：

* BIP16
* BIP30
* BIP34

硬分叉

* BIP50

## Transaction

### 交易的数据结构

交易类型

* P2PKH
* P2SH

签名类型

* SIGHASH\_ALL
* SIGHASH\_NONE
* SIGHASH\_SINGLE
* SIGHASH\_ANYONECANPAY
* 复合签名
  * SIGHASH\_ALL\| SIGHASH\_ANYONECANPAY
  * SIGHASH\_NONE \| SIGHASH\_ANYONECANPAY
  * SIGHASH\_SINGLE \| SIGHASH\_ANYONECANPAY

#### 事务延展性

#### LockTime

#### 交易费和找零

#### CoinBase

#### 标准交易

### 微支付通道

关键点：locktime

### 链下交易

## Contract

## 钱包

## 交易过程

BIP70 - 已过期

BIP21

## P2P网络

### 节点发现

### 节点链接

### initial block download

#### block broadcast

#### 孤儿块

### transaction broadcast

## 节点运行模式

全节点，SPV

## 挖矿方案

## 联合挖矿




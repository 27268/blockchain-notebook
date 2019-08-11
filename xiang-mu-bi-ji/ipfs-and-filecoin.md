---
description: 基于存储证明的区块链
---

# IPFS and Filecoin

## IPFS \(Inter-Planet File System\)

{% embed url="https://github.com/ipfs/specs" %}

### 背景知识

#### 分布式哈希表DHT

#### BitTorrent

#### 版本控制——Git

#### Self-Certified File System

### 系统设计

* Identity
* Network
* Routing
* Exchange
* Object Merkel DAG——数据存储方案
* File——在Merkel DAG之上进行版本化文件系统建模
* Naming: IPNS

### 实现

* libp2p
* IPNS
* IPRS
* IPLD
* Bitswap

## Filecoin

### 复制证明与时空证明

### 去中心化的存储网络

### Storage and retrial market

### Consensus

### go-filecoin 源码

#### 1. 依赖安装的bug

连接不到国外的网站，出现大量类似错误：

```bash
go: golang.org/x/tools@v0.0.0-20190312151545-0bb0c0a6e846: unrecognized import path "golang.org/x/tools" (https fetch: Get https://golang.org/x/tools?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
```

解决方案：加代理

```bash
export GOPROXY=https://goproxy.io
```

ref: [https://www.imooc.com/article/264790](https://www.imooc.com/article/264790)

注意：下面的命令也可以解决对应的问题，但是，当有大量的包不能import时，太太太太太麻烦了！！！

```bash
go mod edit -replace=golang.org/x/lint@v0.0.0-20190313153728-d0100b6bd8b3=github.com/golang/lint@959b441ac422379a43da2230f62be024250818b0
```

#### 教训！！！

```bash
replace (
	golang.org/x/sys v0.0.0 => github.com/golang/sys v1.2.3
	golang.org/x/sys v0.0.0-20171026204733-164713f0dfce => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20180905080454-ebe1bf3edb33 => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20180909124046-d0be0721c37e => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20181116152217-5ac8a444bdc5 => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190215142949-d0b11bdaac8a => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190219092855-153ac476189d => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223 => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190228124157-a34e9553db1e => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190302025703-b6889370fb10 => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190412213103-97732733099d => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
	golang.org/x/sys v0.0.0-20190606165138-5da285871e9c => github.com/golang/sys v0.0.0-20190620070143-6f217b454f45
)
replace golang.org/x/exp v0.0.0-20190121172915-509febef88a4 => github.com/golang/exp v0.0.0-20190510132918-efd6b22b2522
```

## Spacemint



## Stroj






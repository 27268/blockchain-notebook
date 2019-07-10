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

replace golang.org/x/time v0.0.0-20190308202827-9d24e82272b4 => github.com/golang/time v0.0.0-20190308202827-9d24e82272b4

replace (
	golang.org/x/net v0.0.0-20170915142106-8351a756f30f => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20180524181706-dfa909b99c79 => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20180906233101-161cd47e91fd => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20181011144130-49bb7cea24b1 => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20181114220301-adae6a3d119a => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20181201002055-351d144fa1fc => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20190108225652-1e06a53dbb7e => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20190125091013-d26f9f9a57f3 => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20190227160552-c95aed5357e7 => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20190311183353-d8887717615a => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
	golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3 => github.com/golang/net v0.0.0-20190619014844-b5b0513f8c1b
)

replace (
	golang.org/x/oauth2 v0.0.0-20180821212333-d2e6202438be => github.com/golang/oauth2 v0.0.0-20190604053449-0f29369cfe45
	golang.org/x/oauth2 v0.0.0-20190226205417-e64efc72b421 => github.com/golang/oauth2 v0.0.0-20190604053449-0f29369cfe45
)

replace golang.org/x/xerrors v0.0.0-20190212162355-a5947ffaace3 => github.com/golang/xerrors v0.0.0-20190513163551-3ee3066db522

replace (
	google.golang.org/genproto v0.0.0-20180817151627-c66870c02cf8 => github.com/googleapis/go-genproto v0.0.0-20190620144150-6af8c5fc6601
	google.golang.org/genproto v0.0.0-20180831171423-11092d34479b => github.com/googleapis/go-genproto v0.0.0-20190620144150-6af8c5fc6601
	google.golang.org/genproto v0.0.0-20190307195333-5fe7a883aa19 => github.com/googleapis/go-genproto v0.0.0-20190620144150-6af8c5fc6601
)

replace (
	golang.org/x/sync v0.0.0-20180314180146-1d60e4601c6f => github.com/golang/sync v0.0.0-20190423024810-112230192c58
	golang.org/x/sync v0.0.0-20181108010431-42b317875d0f => github.com/golang/sync v0.0.0-20190423024810-112230192c58
	golang.org/x/sync v0.0.0-20181221193216-37e7f081c4d4 => github.com/golang/sync v0.0.0-20190423024810-112230192c58
	golang.org/x/sync v0.0.0-20190227155943-e225da77a7e6 => github.com/golang/sync v0.0.0-20190423024810-112230192c58
	golang.org/x/sync v0.0.0-20190423024810-112230192c58 => github.com/golang/sync v0.0.0-20190423024810-112230192c58
)

replace (
	golang.org/x/crypto v0.0.0-20170930174604-9419663f5a44 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20180505025534-4ec37c66abab => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20180904163835-0709b304e793 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20190211182817-74369b46fc67 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20190225124518-7f87c0fbb88b => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20190228161510-8dd112bcdc25 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20190308221718-c2843e01d9a2 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
	golang.org/x/crypto v0.0.0-20190605123033-f99c8df09eb5 => github.com/golang/crypto v0.0.0-20190618222545-ea8f1a30c443
)

replace (
	golang.org/x/tools v0.0.0-20170915040203-e531a2a1c15f => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20180221164845-07fd8470d635 => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20181117154741-2ddaf7f79a09 => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20181205014116-22934f0fdb62 => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20190121143147-24cd39ecf745 => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20190125232054-379209517ffe => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20190226205152-f727befe758c => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20190311212946-11955173bddd => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
	golang.org/x/tools v0.0.0-20190606124116-d0a3d012864b => github.com/golang/tools v0.0.0-20190620191750-1fa568393b23
)

replace (
	golang.org/x/text v0.0.0-20170915090833-1cbadb444a80 => github.com/golang/text v0.3.0
	golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0
	golang.org/x/text v0.3.2 => github.com/golang/text v0.3.0
)

replace (
	google.golang.org/appengine v1.1.0 => github.com/golang/appengine v1.6.1
	google.golang.org/appengine v1.4.0 => github.com/golang/appengine v1.6.1
)

replace (
	golang.org/x/lint v0.0.0-20190227174305-5b3e6a55c961 => github.com/golang/lint v0.0.0-20190409202823-959b441ac422
	golang.org/x/lint v0.0.0-20190313153728-d0100b6bd8b3 => github.com/golang/lint v0.0.0-20190409202823-959b441ac422
)

replace golang.org/x/exp v0.0.0-20190121172915-509febef88a4 => github.com/golang/exp v0.0.0-20190510132918-efd6b22b2522
```

## Spacemint



## Stroj






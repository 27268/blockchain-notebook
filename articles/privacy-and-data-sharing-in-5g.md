---
description: 'the institution of engineering and technology, 2018'
---

# efficient privacy preserving and data sharing scheme of content-centric network in 5G

{% embed url="https://ieeexplore.ieee.org/document/8320551" %}

要点总结：



## 立题依据

海量网络信息  
 -》用户隐私问题：第三方掌握数据的安全性存在问题 \[6\]  
 -》保障用户对自己数据的访问控制

贡献：  
1. 区块链 + 加密云存储  
2. 放篡改的访问策略，实现用户对数据的完全控制  
3. 实现有效的资源控制，降低网络延时和拥塞程度

相关工作：

* 访问控制 \[7, 8, 9\]：加密数据的访问控制策略研究很少（因为数据源比较复杂）
* 加密算法 in 云计算
  * 基于属性的加密ABE ：用户每次更新访问策略，系统需要根据新的属性集再次加密数据，降低了系统效率，增加了计算开销

## 系统设计

![&#x7CFB;&#x7EDF;&#x67B6;&#x6784;](../.gitbook/assets/image%20%2822%29.png)

* content provider
* social user community
* miner
* blockchain ledger
* cloud storage \(保存密文\)

### content provider

* 制定合理的访问控制策略 access control policy
* 加密信息（社交网络数据）：加密数据，获得数据哈希值，将加密数据上传到云存储网络，提交交易给矿工

access control policy: &lt;IDacc, &lt;IDpro, Nj&gt;, ro, &lt;ts, te&gt; &gt;  
IDacc: identity of the access requestor  
IDpro: identity of the content provider  
Nj: symbol of the record j  
ro: a set of roles. 允许访问：当且仅当 the visitor's label meets the requirements of the data owner，即the requestor is in the role set.  
&lt;ts, te&gt;: time start, time end，允许访问的时间段

![acp&#x793A;&#x4F8B;](../.gitbook/assets/image%20%2840%29.png)

acp1：若 user1 是 user2 的朋友，user1 可以在任意时间访问 user2 的任意信息  
acp2：任意user1都可以在 &lt;ts, te&gt; 时间中，访问 user2 的 N3 数据  
**acp3: a miner signed can read the information form user3 in the time interval ?**

### miner

共识机制：DPOS  
激励：矿工感兴趣的信息

在这种激励策略下，矿工控制着网络中的大多数数据。这使得，当其他用户请求这些数据时，矿工可以在验证访问策略之后，直接将数据分享给用户。

### network user

Common users are divided into groups according to their interests. 不同组的user有不同的标签，同一组中的用户可能满足相同的访问策略。

用户获得信息的两种途径：

* 直接向data owner请求数据，若数据请求者符合data owner的访问控制策略，data owner将解密密钥发送给请求者（访问控制策略的验证和解密过程都会带来计算开销与网络延时）
* 用户向矿工请求数据，由矿工验证用户是否符合请求数据的访问控制策略，若符合，则与用户共享数据——减少网络拥塞和访问时延，可以通过本地缓存（受欢迎数据）提高网络吞吐量

### blockchain ledger

![block&#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;](../.gitbook/assets/image%20%2827%29.png)

hash: sha256  
pointer of record: 信息存储的地址  
provider signature: 用于验证数据的来源与真实性  
sequence number: 数据的唯一索引  
access control policy: 用于过滤非法用户，文本表示形式

### cloud storage

保存数据密文

## 系统实现

1. initialization: 生成用户ID、公私钥、master key 
2. encryption and uploading of data
   1. computation of encryption key: seq = IDPHash\(key\_pri \|\| num \)
   2. generation of transaction: key\_seq = Hash\(key\_mas \|\| seq\)
3. adding blocks to blockchain
4. decryption and downloading of data: 解密密钥key\_seq

![&#x9009;&#x62E9;&#x77FF;&#x5DE5;&#x3001;&#x751F;&#x6210;&#x533A;&#x5757;](../.gitbook/assets/image%20%286%29.png)

性能分析：

* 安全性：来自于key\_seq
  * 外部攻击：外部用户既无法获得解密密钥，也无法通过访问控制策略
  * 针对blockchian的攻击
* 隐私性：访问控制 + 数据加密，密钥管理成本相对较低
* 效率：对称加密 与 ABE 的对比


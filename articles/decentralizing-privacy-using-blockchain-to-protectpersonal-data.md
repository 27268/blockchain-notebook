---
description: 2015 IEEE CS Security and Privacy Workshops
---

# Decentralizing Privacy: Using blockchain to ProtectPersonal Data

要点总结：  
核心是数据授权，数据授权工作通过区块链事务完成。  
数据授权的核心是\(user, service\)实体，生成该实体所需的交互在安全信道中完成。

## 简介

存在的问题：  
大数据时代  
 -》user privacy: 中心化的企业掌握着大量用户信息，个人对自己的数据没有控制权  
 -》最近的一些泄露用户隐私的案例，表明user privacy的严重性

相关的解决方案：  
法律角度：  
技术角度：  
1. OpenPDS - return computations on the data, instead of the raw data.  
2. OAuth protocol - 大型企业的身份验证软件，大型企业作为可信第三方。  
3. 安全算法：  
        k-anonymity, l-diversity, 已知部分数据点或已知高维数据时，可以被反匿名  
        差分隐私、同态计算，效率较低，无法实践应用

贡献点：  
1. 结合区块链与链下存储，构建以隐私为核心的个人数据管理平台。  
2. 平台评估和未来技术讨论：how blockchains could become a vital resource in trusted-computing。

系统解决的隐私问题：

* Data ownership - user拥有并控制自己的个人数据，service仅被授权访问数据
* data transparency and auditability - 哪些数据被收集、如何被访问等，对user透明
* fine-grained access control - user可以更改权限集，撤销之前授予的访问权限。访问控制策略保存在区块链上。（现有授权方案，一旦授权，service可以无限制的访问）

## 方案设计：

![&#x5E73;&#x53F0;&#x6A21;&#x578B;](../.gitbook/assets/image%20%287%29.png)

* 角色
  * user: 下载与使用applications that require processing personal data
  * service: application provider
  * node: 维护区块链与分布式存储（in return for incentive）的实体
* 新增事务类型
  * Taccess：users授权service访问数据
  * Tdata：service or user向node查询数据
* 使用流程：从user首次安装\(that use this platform\)应用开始，
  * 1. new a \(user, service\) identity and send to the block. `Taccess{(user, service), permissions }`
    2. 通过Tdata将user生成的数据以密文的形式发送到区块链上，密文保存在链下存储中，数据指针保存在区块链上
    3. user / service通过Tdata，根据数据指针向node查询数据，\(user验证数字签名，service验证数字签名和访问权限\)
    4. user可以随时使用Taccess修改service的权限

### 网络协议

基础：

* 对称加密方案 \(Genc, Eenc, Denc\)，
  * Generater
  * Encryptor
  * Decryptor
* 数字签名方案\(Gsig, Ssig, Vsig\) —— ECDSA with secp256k1
  * generaor
  * signature
  * verify
* hash function ——sha-256

生成\(user, service\)实体：

![\(user, service\) identity](../.gitbook/assets/image%20%288%29.png)

访问权限/ policy： POLICYu, s = {location, contact}

权限验证：

![&#x6743;&#x9650;&#x9A8C;&#x8BC1;&#x7B56;&#x7565;](../.gitbook/assets/image%20%285%29.png)

Taccess处理策略：验证user身份，修改权限

![&#x9A8C;&#x8BC1;user&#x8EAB;&#x4EFD;](../.gitbook/assets/image%20%284%29.png)

Tdata处理策略：若请求者有权限访问数据，返回数据指针，请求者可以根据指针查找数据（在根据Kad组织的P2P网络中）

![&#x67E5;&#x8BE2;&#x6570;&#x636E;&#x6307;&#x9488;](../.gitbook/assets/image%20%286%29.png)

### 隐私与安全分析


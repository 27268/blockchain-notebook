---
description: IEEE Transactions on Wireless Communications 2018
---

# Distributed resource allocation in blockchain-based video streaming systems with Mobile EC

{% embed url="https://ieeexplore.ieee.org/document/8574049/" %}

要点总结：



立题依据：

流媒体平台存在的问题：内容创造者利润低；收费高、消费者隐私低；广告效果不理想。  
基于区块链技术的视频流媒体时代：内容创造者、广告商、消费者可以在没有第三方介入的情况下互相支持。

基于区块链的视频流系统面临的挑战：视频转码。  
1. 视频转码在视频平台上的必要性；2. 视频转码的计算量大、耗时长、对计算资源要求高\[4, 5\]；  
3. 区块链本身的性能问题，解决方案：自适应块大小

解决上述问题的方向：移动边缘计算  
MEC 将计算密集型的转码任务转移到网络边缘 \[3, 18\]——MEC 节点缺乏协助转码的激励  
—》区块来拿

贡献：

* MEC-enabled framework for blockchian-based video streaming.
* a block size adaptation schema 
* 负载均衡：offload to the nearby MEC, or to a group of D2D users.
* 应用低复杂度的 ADMM-based 算法解决分布式系统中的上述问题。Alternating direction method of multipliers

相关工作：

* 传统视频流
* 带移动边缘计算的区块链


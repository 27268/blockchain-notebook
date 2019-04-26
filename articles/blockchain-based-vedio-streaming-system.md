---
description: IEEE Transactions on Wireless Communications 2018
---

# Distributed resource allocation in blockchain-based video streaming systems with MEC

{% embed url="https://ieeexplore.ieee.org/document/8574049/" %}

要点总结：

优化转码任务在MEC中的分配策略，以追求激励的最大化。

立题依据：

流媒体平台存在的问题：内容创造者利润低；收费高、消费者隐私低；广告效果不理想。  
基于区块链技术的视频流媒体时代：内容创造者、广告商、消费者可以在没有第三方介入的情况下互相支持。

基于区块链的视频流系统面临的挑战：视频转码。  
1. 视频转码在视频平台上的必要性：异构的移动设备、网络和用户首选项。  
2. 视频转码的计算量大、耗时长、对计算资源要求高\[4, 5\]；  
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

* 传统视频流 \[14-18\]
  * Mobile Network: MEC enhanced adaptive bitrate video delivery scheme \(结合内容缓存与ABR\)\[14\]
* 带移动边缘计算的区块链 \[19-23\]

## 模型设计

名词解释：

* MBS: macro base station - 1个
* SBS: small base statiions - M个
* Sb: adaptive block size

### 系统架构

![&#x7CFB;&#x7EDF;&#x67B6;&#x6784;](../.gitbook/assets/image%20%2810%29.png)

1. 用户需要持有一定的token才能进入系统。
2. user 与 SBS 的数量服从齐次泊松点过程。 直观上，只要随机事件在不相交时间区间是独立发生的，且在充分小的时间区间上只发生一次，它们的累计次数就是一个泊松过程。
3. user node 和 SBS 节点都可以执行解码任务。每个SBS 中有一个broadcaster BCm，m in M。

### 视频转码模型 video transcodeing model

Transcoding job transaction TJvm: &lt;Im, Vm, Qvm, Lvm&gt;.    
其中，Im表示原始视频流文件的大小，Vm表示Vm个不同版本的 Qvm个 长为Lvm的segment数组。  
\[seg1, seg2, ... , segQvm\], 每个seg长为Lvm，共有Vm个这样的数组。

TJvm被加入到block之后，the next blockhash被用来选择 完成当前block中的转码工作的transcoder，每个TJvm都有一一对应的transcoder TCvm来完成转码工作。

用 \(rvm, tvm\) 描述第vm个版本的要求，rvm: bitrate, tvm: delay tolerance

### Computation offloading model

![TJvm&#x4E2D;&#x7684;&#x8F6C;&#x7801;&#x4EFB;&#x52A1;](../.gitbook/assets/image%20%2821%29.png)

参数分别为：size of video segment in bit, workload/intensity in CPU cycles/bit, number of video segments, length of video segment, required bitrate \(bit/s\), delay tolerance in s.

假设所有的SBS与node都可以执行转码任务。Two offloading mode：

* Mode 0: offload to a nearby SBS: 将整个转码任务分配给SBS m
* Mode 1: offload to a group of D2D nodes: 将转码任务平均分成Kvm部分，分配给nearby D2D nodes

由TCvm选择offloading mode。

### network model

* 移动蜂窝网络
* D2D网络

## Offloading framework and incentive mechanism

offload framework的性能分析，基于区块链的视频流系统激励机制

### offloading framework

性能指标：

* output size
* delay
  * transcoding delay 
  * queuing delay: 区块链消息排队 + 转码排队@SBS，video segment的到达速率服从泊松分布
  * delay for sending the transcoded version back
* energy consumption

1. mode 0: offload to a nearby SBS 的性能指标分析
2. mode 1: offload to a group of D2D users 的性能指标分析

### incentive mechanism

* the revenue of transcoding service
  * a percentage of block reward shared by the bonded nodes
  * other rewards and punishment: the transcoding reward, fee share, slashed reward
* the cost of transcoding service: the energy cost for the transcoding task

![&#x6536;&#x76CA;](../.gitbook/assets/image%20%2842%29.png)

为了激励更多的用户 become transcoders to perform transcoding for the broadcasters, 本文希望 transcoder能获得最大的平均转码收益 —》优化问题

* adaptive block size
* scheduling of offloading: offloading decision indicator vector
* computational resource allocation profile
* spectrum allocation profile 频谱分配（频分多路复用）

![&#x4F18;&#x5316;&#x76EE;&#x6807;&#xFF0C;&#x9650;&#x5B9A;&#x6761;&#x4EF6;&#x89C1;&#x8BBA;&#x6587;](../.gitbook/assets/image%20%281%29.png)

这个优化是背包问题的扩展：系统总处理能力一定，如何处理更多的任务以获得最大的收益。  
—》简化问题，并求解（alternating direction method of multipliers **ADMM**）

* 自适应块大小
* Offloading Scheduling and Resource Allocation
* Stopping Criterion
* Discrete Variables Recovery


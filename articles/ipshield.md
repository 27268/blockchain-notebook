---
description: >-
  https://github.com/nesl/ipShield, 11th USENIX Symposium on Networked Systems
  Design and Implementation (NSDI), 2014
---

# ipShield - A framework for enforcing context-aware privacy

{% embed url="http://nesl.github.io/ipShield/" %}

要点总结：



#### 立题依据：

智能手机上大量的传感器：privacy-prone sensors \(GPS, camera, bluetooth, etc\), innocuous sensors\(加速器、陀螺仪、红外传感等\)  
 -》传感器引起的隐私问题：keylogging attacks, reconstruction of travel trajectories, 通过共享信息推导出敏感数据；此外用户只能选择接受所有权限请求，或者放弃使用服务。  
 -》现有方案：TaintDroid，MockFroid，  
      -》TaintDroid：扩展安卓系统，在敏感信息中添加污染位，通过第三方应用追踪这些位的流向。不足：运行时开销大；且数据是否敏感多依赖上下文，灵活性差；不提供隐私保护建议  
      -》MockDroid：仅适用于应用程序的显示请求；可以模拟资源或提供对资源的访问，但不提供模拟哪些传感器的建议  
      -》PMP：提供运行时的资源访问控制，不处理传感器数据

#### 贡献：

* 修改Android系统：监控应用程序对传感器的访问（包括innocuous sensors）
* 以用户更容易理解的格式展示隐私风险：列出app所使用传感器提供的信息可以进行哪些推断（可能泄漏哪些信息）用户可以声明白名单和黑名单指定privacy preferences
* 提供推荐引擎：将上述黑名单和白名单转换为对传感器的访问控制（允许/拒绝）
* provide the user with options to configure context-aware fine-grained privacy actions on different sensors on a per app basis at runtime. 

ipShield的CPU和内存开销可以忽略不计、reduction in battery life is 8%.

#### 隐私案例研究：

* transportation mode and keylogging: 加速器和陀螺仪——活动识别算法被用来识别用户的运动模式（walking, motorized or still）
* Saga: 位置传感器——后台运行，跟踪用户的位置轨迹，从而推断日均通勤时间、工作时间、家庭位置、办公室位置、医院位置等。

#### inference privacy problem

用户授权访问的非敏感数据也可以用来推断用户敏感信息。  
inference - labels associated with data, such as activity, behavior, places. 将相同语义类型的标签分组到一个推理类别（inference category）中。  
sensitive categories and their labels \(to keep private\) from the blacklist  
输入：whitelist and blacklist of prioritized inference categories  
输出：privacy actions on sensors 

side-channel attacks：利用加密算法执行过程中暴露出来的信息来恢复密钥。信息包括：运行时间、缓存行为、功耗、硬件发出的物理信号（击键声、电磁波）等

inference privacy attack与side-channel attack的区别：

* 数据来源：前者传感器，后者物理信号、隐蔽信息
* 数据接收方：前者接收者就是adversary，后者adversary is typically different from the intended recipient
* 安全措施：后者可以将计算机硬件放到物理上隔离的、安全的腔体中从而降低风险，而前者不行

#### 相关工作

![&#x76F8;&#x5173;&#x5DE5;&#x4F5C;](../.gitbook/assets/image%20%2811%29.png)

#### android的传感器数据流路径和安全模型

![android &#x4F20;&#x611F;&#x5668;&#x6570;&#x636E;&#x6D41;&#x6A21;&#x578B;](../.gitbook/assets/image%20%2813%29.png)

安全模型：

* application sandboxing
* Secure IPC
* access control using Manifest

## 架构设计


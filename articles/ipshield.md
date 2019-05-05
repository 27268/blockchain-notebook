---
description: >-
  https://github.com/nesl/ipShield, 11th USENIX Symposium on Networked Systems
  Design and Implementation (NSDI), 2014
---

# ipShield - A framework for enforcing context-aware privacy

{% embed url="http://nesl.github.io/ipShield/" %}

要点总结：

传感器的权限控制

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

![&#x76F8;&#x5173;&#x5DE5;&#x4F5C;](../.gitbook/assets/image%20%2829%29.png)

#### android的传感器数据流路径和安全模型

![android &#x4F20;&#x611F;&#x5668;&#x6570;&#x636E;&#x6D41;&#x6A21;&#x578B;](../.gitbook/assets/image%20%2832%29.png)

数据流：

SensorService, LocationManagerService在Android系统boot时启动的系统服务。

安全模型：

* application sandboxing
* Secure IPC
* access control using Manifest

## 架构设计

设计目标：

* better monitoring of sensor access
* meaningful privacy abstraction
* privacy rule recommendation 
* fine-grained control over shared data

### 系统模块

![ipShield &#x6570;&#x636E;&#x6D41;](../.gitbook/assets/image%20%2819%29.png)

* 数据库
  * sensor counter and database \(F\): 操作系统监控应用访问传感器的次数，安装新应用或者卸载应用时，更新数据库。
  * **inference database \(E\)** : map the list of **inference categories** and their labels. Inference categories can be predicted using a combination of sensors, the prediction accuracy and the ML algorithm. —— 与杀毒软件公司维护的病毒库类似

![inference DB](../.gitbook/assets/image%20%2831%29.png)

* 上下文引擎 context engine \(D\) : 一组机器学习算法，将原始传感器数据作为输入，output the current context label. context label 与 inference label相同，区别在于：inference label是对手通过shared data推理得到的。用户可以通过配置privacy rules 触发context label.
* firewall-manager: 与用户交互，并生成privacy rules。
  * semantic firewall configurator \(G\)
    * 输入：app访问的传感器，从inference DB中查询的可能的inference category
    * 输出：根据inference category 配置的白名单和disjoint blacklist
  * direct firewall configurator \(I\): 允许用户配置fine-grained context-aware rules.
  * rule recommender \(H\)
    * 输入：the user's privacy preference \(以上述白名单和黑名单的形式\)
    * 输出：the actual privacy actions on the sensors \(Normal正常, Suppress禁止\) -&gt; provide user with flexibility to override them\(允许/禁止\).
  * configurator switcher \(J\): 允许用户在语义模式和直接配置模式之间切换配置规则
  * 由J 决定I or G 作为 H 的输入，生成recommending sensor combination，输出向量表示应该启用或者禁用哪些传感器
* rule-based Obfuscator 基于规则的混淆器：实现了不同的隐私操作。根据privacy rules, sensor data, app，apply the appropriate rules to the data before releasing them.

### 隐私规则分类

隐私规则的组成部分：

* context
* SensorType
* Action

![&#x9690;&#x79C1;&#x89C4;&#x5219;&#x7684;&#x9009;&#x9879;&#x5217;&#x8868;](../.gitbook/assets/image%20%2840%29.png)

隐私规则的一般形式：if\(contexts\), apply Action to SensorType.  
eg：if \( \(TimeOfDay in \[10am-5pm\]\) ^ \(Place = school\) ^ \(AppName = facebook\) \), apply 'Suppress' on 'GPS'.

* Normal action: release data without any changes.
* Suppress action: the app is unable to detect any sensor event.
* Constant action: replace actual data with a constant value.
* Perturb action: add noise to sensor data
* Play-back: suppress the data from the actual sensor hardware, 将合成的传感器测量数据聪外部服务发送到请求程序

### 规则推荐

目标：inference labels in the whitelist are allowed, in the blacklist are blocked.  
输入：whitelist and blacklist of the inference categories  
输出：a configuration for enabling or blocking of sensors accessed by an app.

#### 问题格式化

记N为app使用的传感器数量 \(get from Sensor Counter\), 传感器向量 s=\[s1, s2, ... sN\], 其中si表示第 i 个传感器的状态 \( 0 - 禁用，1 - 允许 \)。用 L={ l1, l2, ... lL } 表示interface categories.

定义映射 M: {0,1}^N \* L -&gt; \[0, 1\]，M\(s, l\)=0 表明不存在学习算法 that **use the data stream** from the sensors enabled in s, and **infer a label** in category l，M\(s, l\)=1表示能完全推断出来。学习算法不受限制。

映射 M 可以从 inference DB 中获得。学习算法work on features extracted from raw sensor data。由于sensor data 是可以共享的，所以任何特征都可以从原始数据中提取出来。

记 pl in {0, ... , Pmax} 为用户设置的优先级，pl越大，优先级越高，用户越不希望被泄漏

![&#x4F18;&#x5316;&#x95EE;&#x9898;](../.gitbook/assets/image%20%2843%29.png)

s.t. 使得，满足 such that, or subjective to。

穷举可能的状态空间s，找能使公式1取最大值的s，

#### example

L = {transportation mode, location, onscreen taps},   
其中，W = { transportation mode }, B = {location, onscreen taps }  
若用户设置的优先级为 p1 = {10, 4, 10}，即p\(transportation mode\)=10, p\(location\)=4, p\(onscreen taps\)=10

选能使如下式子最大化的sensor combination：  
M\(GPS+Acc+Gyro, transportation mode\) \* 2^\(10\) -  M\(GPS+Acc+Gyro, location, onscreen taps\) \* 2^\(4, 10\),  
 

## 系统实施与评估

![&#x7CFB;&#x7EDF;&#x5B9E;&#x65BD;](../.gitbook/assets/image%20%287%29.png)

信任模型：第三方应用不可信，但第三方应用之间不会互相共享信息；linux内核与内核实现在沙箱中的应用可信；操作系统库与系统服务运行在应用沙箱中，也可信。

代码实现：

* Sensor counter：file
* Inference：提供接口给人们贡献inference category
* context engine：需要访问传感器原始数据——（HAL -&gt; context engine -&gt; Sensor Service），对访问速度有要求
* FirewallManager


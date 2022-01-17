---
title: 【论文笔记】Automatic Virtual Network Embedding - A DRL Approach with GCN
author: Gemini向光性
date: 2020-7-13
categories: 
  - RP - 科研论文
tags: 
  - Paper
  - NFV
  - DRL
  - GNN
mathjax: true
# top: true
---

本篇论文将强化学习A3C算法与图卷积神经网络GCN相结合，并且设置了多目标的奖励函数，提出了一种更加高效的虚拟网络嵌入算法。

## 论文简介

**论文名称**：Automatic Virtual Network Embedding: A Deep Reinforcement Learning Approach with Graph Convolutional Networks  
**论文作者**：Zhongxia Yan, Jingguo Ge, Y ulei Wu, Senior Member , IEEE, Liangxiong Li, Tong Li  
**发表期刊**：JSAC-2020 (CCF-A)  
**研究方向**：NFV 网络功能虚拟化  
**关键技术**：虚拟网络嵌入, 强化学习, 图卷积神经网络  
**主要创新**：强化学习结合图卷积神经网络、并行的强化学习框架、多目标的奖励函数  
[下载论文](https://ore.exeter.ac.uk/repository/bitstream/handle/10871/40799/Yan-JSAC-2020.pdf?sequence=1&isAllowed=y)

<!-- more -->

**词汇缩写**

| 缩写 | 描述 | 全名 |
| :---:| --- | --- |
VNE | 虚拟网络嵌入 | Virtual Network Embedding
VNR | 虚拟网络请求 | Virtual Network Request
RL  | 强化学习     | Reinforcement Learning
GCN | 图卷积神经网络 | Graph Convolutional Network
A3C | | Asynchronous Advantage Actor-Critic

## 问题建模

### 底层网络

加权无向图 $G_s = (N_s, L_s, A^n, A^l)$

- $N_s$：物理节点的集合
- $L_s$：物理链路的集合
- $A^n$：节点属性，如CPU processing capability, memory space and node reliability
- $A^l$：链路属性，如bandwidth, latency and packet loss rate

本文仅考虑CPU资源和带宽资源分别作为节点和链路的属性

### 虚拟网络请求

虚拟网络：加权无向图 $G_v = (N_v, L_v, R^n, R^l)$

- $N_v$：虚拟节点的集合
- $L_v$：虚拟链路的集合
- $R_n$：虚拟节点请求的集合
- $R_l$：虚拟链路请求的集合

虚拟网络请求：$VNR = (G_v, t_a, t_d)$

- $t_a$：VNR到达时间
- $t_d$：VNR离开时间

### 问题约束

映射问题 $M: G_{v}\left(N_{v}, L_{v}\right) \rightarrow G_{s}^{\prime}\left(N_{s}^{\prime}, L_{s}^{\prime}\right)$

- 节点映射：CPU资源约束
- 链路映射：带宽资源约束

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-ex-problem.png)

### 优化目标

#### 接受率

底层网络在时间$T$接受率为

$$AC_Ratio(T) = \frac{\sum_{t=0}^{T}{NUM\_VNR\_S}}{\sum_{t=0}^{T}{NUM\_VNR}}$$

- $NUM\_VNR\_S$：成功嵌入的VNR数量
- $NUM\_VNR$：VNR的总数量

#### 长期平均收益

单个VNR $G_v$ 被嵌入成功的收益为

$$\operatorname{Rev}\left(G_{v}\right)=\sum_{n_{v} \in N_{v}} \operatorname{CPU}\left(n_{v}\right)+\sum_{l_{v} \in L_{v}} B W\left(l_{v}\right)$$

底层网络长期平均收益为

$$Rev(T) = \frac{\sum_{t=0}^{T}Rev(G^t_v)}{T}$$

- $G^t_v$：表示时间$t$是被成功嵌入的VNR

#### 运行时间

权衡性能与时效，适应实时场景。

### VNE

## 算法模型

为了适应RL框架，本文Agent使用单步决策，即每一次决策生成的是一个虚拟节点的放置策略

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-rl-framework.png)

当获取一个虚拟节点的放置策略后，尝试将其进行放置

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-1-vne-procedure.png)

### 环境 Environment

由底层网络及其相关设定构成

### 状态 State

所需状态表示如下：

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-state.png)

### 动作 Action

在Agent生成单个虚拟节点的放置决策后，需尝试对其进行放置，此时要满足以下约束

- 被选物理节点可用资源 > 当前虚拟节点资源请求
- 存在带宽资源充足的链路来放置虚拟链路

对于链路的搜索，本文采用混合搜索策略：优先尝试最短路径，若失败则尝试其他次优路径。

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-ex-hybrid-search.png)

### 代理 Agent

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-rl-agent.png)

#### GCN

#### 策略生成

#### A3C 并行训练

#### Master

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-2-training-master.png)

#### Worker

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-algo-3-training-worker.png)

### 奖励 Rewords

- 动作反馈 action feedback

$$r_{a}=\left\{\begin{array}{cl}
  100 \gamma_{a} & a_{t} \text {is successful} \\
  -100 \gamma_{a} &  \text {otherwise}
\end{array}\right.$$

- 成本效益 cost-efficient
  
$$r_{c} =  \frac{\delta(revenue)}{\delta(cost)}$$

- 负载均衡 Load balancing
  
$$r_{s} = \frac{S_CPU_Remaining[a]}{S_CPU_Max[a]}$$

- 资格痕迹 eligibility trace

$$e g b_{-}{trace}_{t}[i]=\left\{\begin{array}{cl}
  \gamma_{e}\left(e g b_{-}{trace}_{t-1}[i]+1\right) & i==a_{t} \\
  \gamma_{e} {egb}_{-} {trace}_{t-1}[i] & \text { otherwise }
\end{array}\right.$$

故最终对于动作$a{t}$奖励函数（Reward Function）为

$$Reward[a_{t}]=\frac{r_{a} r_{c} r_{s}}{e g b{-}trace[a_{t}]+\epsilon}$$

## 性能评估

### 设计实验

- 底层网络拓扑  
  - 使用参数 $\alpha = 0.5$ 和 $\beta = 0.2$ 的 Waxman 随机图来生成一个底层网络拓扑
  - 该网络具有100个结点和500条边（模拟一个中型的ISP）。
  - 随机分配每个节点的CPU数量和边的带宽大小为50~100个单位。
- 虚拟网络请求  
  - 随机生成VNR时满足Possion process（泊松分布），每组评估持续50000个时间单位
  - 即当VNR预期到达率为4个/100个时间单位时，则约有2000个VNR
  - 每个VRN的生成周期满足平均值为500的指数分布
  - 每个VNR的数量均匀分布在2~10之间
  - 初始化VNR中的节点CPU需求和链路带宽需求为0~30的均匀分布
  - 每对节点有50%的可能性形成边

- 动态设置参数
  - VNR的到达率、节点及链路资源的分配、每个VNR的节点数量
  - 我们可以通过调节这三个参数来评估各种VNE场景
- 测试阶段
  - 学习代理仅使用actor网络生成嵌入策略，来从底层网络拓扑中选择合适的节点托管当前的虚拟节点
  - 该代理已被训练72小时，经历了70000次训练迭代，进行了近1680000次不同的VNR

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-exp-parameter-settings.png)

### 对比算法

- R-ViNE  
  使用基于确定的取整（rounding-based）的方法来获得与VNE问题对应的MIP的线性规划松弛（linear programming relaxation），以最小化VNR的成本
- D-ViNE  
  和RR-ViNE，但特殊在其取整方法是随机的
- GRC  
  一种基于全局资源容量管理的节点排序算法
- MCVNE  
  一种基于强化学习的 Monte-Carlo MCTS 动作空间搜索算法
- NodeRank  
  一种节点排序算法，灵感来自与Google的PageRank算法  

它们基本覆盖了当前大部分算法的观点

### 评估指标

#### VNR 到达率测试

实际场景：虚拟网络总在忙碌时频繁接受VNR请求，空闲时则反之。  
实验模拟：将到达率由4个/100时间单位逐渐增至20个/100时间单位，步长为2。  

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-1.png)

结果分析：该算法在VNR请求较频繁时，接受率和平均收益明显优于其他算法。  

#### 资源请求测试

实际场景：不同的网络服务具有不同的资源需求模式，比如：

- 计算密集型任务需要更多的节点资源（CPU）
- 而通信密集型任务需要更多的链路资源（带宽）

实验模拟：将节点与链路资源需求的逐渐由[0,30]升至[0,100]的平均分布，步长为10。

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-2.png)

结果分析：随着资源需求越来越多，嵌入的成功率也会都明显随之降低，但该算法的表现依然是最忧的。

#### 节点数量扩展性测试

实际场景：企业级用户对网络服务需求量较大，个人用户服务使用的节点数量较小。  
实验模拟：将VNR中的虚拟节点数量从[2,10]的均匀分布增加到[2,32]，步长为2。

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-3.png)

结果分析：该算法优势明显，但可以发现当节点逐渐增加时，接受率下降明显

原因分析：

- 因为每个VNR都必须作为一个整体成功地嵌入；一个更大的VNR意味着在嵌入的中间步骤中失败的机会更多；
- 因为同一VNR中的两个虚拟节点不能共享一个特定的基板节点，单个VNR中的更多节点限制了候选动作空间

#### 平均运行时间统计

平均运行时间指VNE算法处理一个完整VNF的平均时间开销

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-4.png)

### 验证测试

在相同条件下，与其他基于强化学习的VNE算法进行比较

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-8.png)

#### 训练效率及收敛性

不同算法在相同实验条件下的训练效果对比，见下图(a)  
实验结果：可以发现，在这组实验中，平均收益的优势要比接受率的明显  
原因分析：当VNR的节点数量较少时，所有算法都可以有很好的表现；但当节点增多后，该算法可以带来更多的潜在收益（多指标的Reward）。

#### 资源请求测试验证

在不同的资源请求情况下，对这些算法进行测试，见下图(b)  

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-5.png)

实验结果：在不同数量的资源请求情况下，该算法的接受率均是最优的；此外，可以发现CNN的性能表现始终是最差的  
原因分析：用GCN代替传统的CNN进行特征提取可以带来更好地性能。

### 其他参数下模型的可行性研究

#### 附加网络拓扑和参数

CSTNET：中国网络运营商，见下图(a)

- 红色边为100Gb带宽的链路
- 绿色边为10Gb带宽的链路
- 橙色边为2.5Gb带宽的链路
- 黑色边为1Gb带宽的链路
- 边的权重：平均传输延迟 average transmission latency（毫秒），

对比算法：Noderank  
实验设计：

- 模拟了一些随机的点对点的数据传输任务
- 数据传输速率为 [500Mbps,3Gbps] 的均匀分布

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-6.png)

实验结果：A3C+GCN 模型的延迟更少

#### 其他指标

- 节点资源利用率：VNRs使用的节点基板资源量/资源总量
- 链路资源利用率：VNRs使用的链路基板资源量/资源总量

实验设计：同平均到达率测试

![ ](/resource/images/paper/paper-nfv-vne-drl-gcn-7.png)

实验结果：A3C+GCN算法的这两项指标均为最佳

## 主要创新

- 基于RL+GCN的自动虚拟网络嵌入算法
- 并行的策略梯度训练方法
- 多指标的奖励函数

## 总结思考

- GCN较CNN可以更好地提取非欧数据的特征
- A3C算法较其他RL算法性能表现更佳
- 并行的梯度训练策略更适应于实际场景
- 多指标的Reward可以让Agent学习到更好地策略来提高收益
- 在模型评估部分，实验的设计比较完善

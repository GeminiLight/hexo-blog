---
title: 【论文笔记】Latency-aware VNF Chain Deployment with Efficient Resource Reuse at Network
author: Gemini向光性
date: 2021-1-13
categories: 
  - RP - 科研论文
tags: 
  - Paper
  - NFV
mathjax: true
---

本文研究了网络边缘场景下的VNF放置问题，旨在最小化整体（服务器和链路）资源消耗，提出了一种延迟感知的两阶段方案，即搜索路径的约束深度优先搜索算法（CDFSA） 和部署VNF的基于路径的贪心算法（PGA）。

## 论文简介

**论文名称**：Latency-aware VNF Chain Deployment with Efficient Resource Reuse at Network Edge  
**论文作者**：Panpan Jin, Xincai Fei, Qixia Zhang, Qixia Zhang, Fangming Liu, Bo Li  
**发表会议**：INFOCOM 2020 (CCF-A)  
**研究方向**：NFV 网络功能虚拟化  
**关键技术**：邻接矩阵重构, 深度优先搜索, 贪婪算法  
**主要创新**：网络边缘场景、延迟保障、两阶段（先边后点）  
[下载论文](https://ieeexplore.ieee.org/document/9155345)

<!-- more -->

**缩写词汇表**

| 缩写 | 描述 | 全名 |
| :---:| --- | --- |
VNF | 虚拟网络功能 | Virtual Network Function
SFC | 服务功能链   | Service Function Chain
AP  | 接入点   | Access Point

## 研究背景

- 研究问题：VNF部署问题
- 研究场景：网络边缘
- 优化目标：在满足延迟要求的前提下，最小化服务器和链路的资源消耗
- 研究难点：需同时考虑服务器和链路资源消耗
  - Path 1：虽选择了最短路径，但由于$d_2$没有VNF2需要即时运行，这会产生额外的计算资源消耗
  - Path 2：虽避免了新开服务器，但链路要长于Path 1

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-problem.png)

## 问题建模

### Network

无向图 $\mathbb{G} = (\mathbb{U}, \mathbb{D}, \mathbb{E})$

- $\mathbb{U}$：Users，表示用户的集合
- $\mathbb{D}$：Devices，表示网络边缘中所有服务器硬件设备的集合，如接入点(ccess points)、交换器(switches)和路由器(routers)等
  - 对于一个设备$d_i \in \mathbb{D}$，其计算资源为$C_i$
- $\mathbb{E}$：Edges，表示服务器间物理链路的集合
  - 对于边$e(u,v) \in \mathbb{E}$，表示设备$d_u$和$d_v$间链路，其带宽资源为$B(u,v)$

### SFC Request

不同的用户有不同的服务需求，每个SFC由一组由顺序的VNF序列组成

- $\mathbb{N}$：表示各种类型VNF的集合
- $\mathbb{S}$：表示各种SFC的集合
- $\mathbb{P}$：表示每个SFC实际部署链路的集合

对于用户$u_i \in \mathbb{U}$其请求的SFC为$S_i$，

- 其中VNF个数为$|S_i|$，第$l$个VNF为$s_i^l \in \mathbb{S_i}$
- $\Phi_i^{ll'}$表示$s_i^l$与$s_i^{l'}$间的子链
- $T_{i,n}^{l}$表示第$l$个VNF的类型是不是$n \in \mathbb{N}$
- $(\phi_i, t_i, r_i)$分别表示$S_i$的source，destiination和flow rate
  - $\phi_i$是一组$S_i$的AP的集合，它们分布在不同的边缘云上，距离用户$u_i$非常近
  - 每个SFC只能选择一个$\phi_i$种AP来进行网络访问，如第$k$个AP $d_{i,k} \in \phi_i$
  - 每一个AP $d_{i,k}$实质仍对应一台服务器$d_j \in \mathbb{D}$，其处理能力表示为$\alpha_j$
  - 对于$r_i$，假设流速始终不变，且为单流
- $p_i \in \mathbb{P}$是该SFC被放置的物理链路的集合
  - 如$\{e(d_1, d_2), e(d_2, d_3), e(d_3, d_4)\}$

### 问题约束

#### 计算资源约束 Resource Capacity

- 一台服务器上的VNF可以同时被不同的SFC使用，使得它的剩余处理能力得到利用
- 对于服务器$d_j \in \mathbb{D}$，其运行的VNF种类的数量为$Y_{i,n}$
- 其中，对于类型为$n$的VNF，其所消耗的计算资源为$R_n$
- 当服务链所需VNF类型已经运行在$d_j$上时，不需新开VNF，即$Y_{i,n}$不变；否则，新开一个VNF实例且$Y_{i,n} = Y_{i,n} + 1$

每台服务器$d_j$运行实例所需计算资源不超过其最大计算资源$C_j$：

$$\sum_{n \in \mathbb{N}} Y_{j, n} R_{n} \leq C_{j}, \quad \forall d_{j} \in \mathbb{D}$$

#### 链路资源约束 Bandwidth Capacity

同样，每条链路$e(u,v)$所负载的带宽资源不超过其最大容量$B(u,v)$：

$$
\sum_{S_{i} \in \mathbb{S}} \sum_{\Phi_{i}^{l l^{\prime} \subseteq S_{i}}} Z_{i(u, v)}^{l l^{\prime}} \cdot r_{i} \leq B(u, v),
\forall e(u, v) \in \mathbb{E}, 1 \leq l<l^{\prime} \leq\left|S_{i}\right|$$

- $Z_{i(e,u)}^{ll'}$：表示服务链$S_i$的子链$\Phi_i^{ll'}$是否经过链路$e(u,v) \in \mathbb{E}$

#### 处理能力约束 Processing Capacity

每台服务器$d_j$对所有VNF的实际处理能力不超过其最大能力：

$$\sum_{S_{i} \in \mathbb{S}} \sum_{s_{i}^{l} \in S_{i}} T_{i, n}^{l} \cdot X_{i, j}^{l} \cdot r_{i} \leq Y_{j, n} \cdot W_{n}, \forall n \in \mathbb{N}, \forall d_{j} \in \mathbb{D}$$

- $W_n$：对type-n VNF的处理能力
- $X_{i,j}^l$：表明SFC $S_i$ 的第$l$个$VNF$是否运行在服务器$d_j$上
  - 特别地，此处假设SFC为单流，即SFC中的一个VNF只能运行在一个服务器上
  - $\sum_{d_{j} \in \mathbb{D}} X_{i, j}^{l}=1, \quad \forall S_{i} \in \mathbb{S}, \forall l \in\left[1,\left|S_{i}\right|\right]$

#### 延迟约束 Latency Limitation

本文考虑了两种延迟：

- **排队延迟**（queueing delay）
  - $q_j$：SFC在AP $d_j$ 处排队产生的延迟
  - 对于每个AP，将其建模为一个$M/M/1$队列，则其满足Little law
  - $q_{j}=\frac{1}{\alpha_{j}-\sum_{S_{i} \in \mathbb{S}} A_{i, j} \cdot r_{i}}$
    - 式中，$A_{i,j}$表示$S_i$是否从$d_j$接入网络
    - 每个SFC仅有一个AP，故有$\sum_{d_{j} \in \mathbb{D}} A_{i, j}=1, \quad \forall S_{i} \in \mathbb{S}$
- **传播延迟**（propagation delay & transmission delay）
  - $l_i(u,v)$：SFC $S_i$在链路$e(u,v)$上进行传输产生的延迟

故总延迟为：

$$\begin{aligned}
\sum_{d_{j} \in \mathbb{D}} A_{i, j} \cdot q_{j}+\sum_{\Phi_{i}^{l \prime} \subseteq S_{i}} Z_{i(u, v)}^{l l^{\prime}} \cdot l_{i(u, v)} \leq \beta_{i} \\
\forall e(u, v) \in \mathbb{E}, \forall S_{i} \in \mathbb{S}, 1 \leq l<l^{\prime} \leq\left|S_{i}\right|, j=u \text { if } l=1
\end{aligned}$$

#### 运行约束 Running VNF

对于$S_i$，其所有VNF都应当被运行

$$\sum_{s_{i}^{l} \in S_{i}} \sum_{d_{j} \in \mathbb{D}} X_{i, j}^{l}=\left|S_{i}\right| \quad \forall S_{i} \in \mathbb{S}$$

### 优化目标

同时最小化服务器和链路的资源消耗

$$\begin{array}{cc}
\min \sum_{d_{j} \in \mathbb{D}} \sum_{n \in \mathbb{N}} Y_{j, n} \cdot R_{n}+\sum_{e(u, v) \in \mathbb{E}} \sum_{S_{i} \in \mathbb{S}} \sum_{\Phi_{i}^{l l^{\prime}} \subseteq S_{i}} Z_{i(u, v)}^{l l^{\prime}} \cdot r_{i} \\
\text { s.t. } (1),(2),(3),(4),(5),(6),(7),(8)
\end{array}$$

## 算法模型

### Overview

#### 问题难点

- 该问题是NP-hard问题
- 无法预测未来的SFC，只能最小化当前SFC的资源消耗

#### 解决方案

两阶段：选择路径 -> 部署VNF

对于$S_i = (\phi_i, t_i, r_i)$，

1. **选择路径**：寻找从$\phi_i \rightarrow d_i$的满足约束的所有预规划路径$P_i^{\Lambda}$
2. **部署VNF**：对于每个路径$p \in P_i^{\Lambda}$，尽可能多的复用已运行的VNF来进行实际部署
3. **最优方案**：选择其中资源消耗最少的方案

### 选择路径

路径需满足

- 从$\phi_i$开始到$t_i$结束
- 实际延迟不超过延迟限制
- 每条边都满足可用带宽约束

#### 构建矩阵

首先，构造无向加权图（见(a)）$\mathbb{G}^{\prime}=\left(\mathbb{D}, \mathbb{E} ; w_{1}, w_{2}\right)$，$w_1$和$w_2$分别表示该边的延迟和剩余带宽

#### 重构矩阵

为了找到网络中所有对服务链$S_i = \{s_i, s_2, \cdots, s_{|S_i|}\}$可用的服务器和链路，本文构建了一个基于$\mathbb{G}^{\prime}$加权混合多图$\mathbb{H}^{\prime}=\left(\mathbb{D}', \mathbb{E}' ; w\right)$（见(b)），$w$是指每条边的延迟。重构步骤如下：

##### 考虑服务器约束

首先将$S_i$的所有AP $d_j \in \phi_i$加入到$\mathbb{H}$，其它的服务器需满足以下条件之一才会被加入$\mathbb{H}$：

1. 其剩余计算资源大于$S_i$所需的最小计算资源$R_{min}^{i}$（整条SFC中计算资源需求最小VNF的值）
2. 它正在运行着的VNF中有SFC所需的VNF类型

##### 考虑链路约束

- 一个服务器可能会被部署两次，部署路径可能会出现环
- 为了达到优化目标，每一条边仅可被通过2次，且需不同方向

每条链路$e(u,v)$的最大通过次数有三种情况

- 2次：当$\left\lfloor\frac{\left.B_{(} u, v\right)}{r_{i}}\right\rfloor>1$即剩余带宽资源大于需求时，加入权值为延迟$l(e,v)$的有向边$(u,v)$和$(v,u)$到$\mathbb{H}$
- 1次：当$\left\lfloor\frac{\left.B_{(} u, v\right)}{r_{i}}\right\rfloor=1$即剩余带宽资源等于需求时，加入权值为延迟$l(e,v)$的无向边$(u,v)$到$\mathbb{H}$
- 0次：当$\left\lfloor\frac{\left.B_{(} u, v\right)}{r_{i}}\right\rfloor<1$即剩余带宽资源不足时，不加入$\mathbb{H}$

##### 服务器通路验证

如果没有边与某服务器相连，就从$\mathbb{H}$中移除它。

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-algo-reconstruct-graph.png)

#### CDFSA

Constrained Depth First Search Algorithm

**链路邻接矩阵 $M$**：存储$\mathbb{H}$中的信息

$$M[u][v]=M[v][u]=\left\{\begin{array}{l}
0, \text { if there is no link, } \\
1, \text { if there is an undirected link } \\
2, \text { if there are two directed links. }
\end{array}\right.$$

**链路延迟矩阵 $E$**：其每个元素$E[u][v]$表示链路$(u,v)$的延迟为$l(u,v)$

**算法流程**

- 初始化：邻接矩阵M和延迟矩阵E
- 对于每一个AP $d_j \in \phi_i$，
  - 当前节点 $u=d_j$
  - 总延迟 $L_t$ += $q_j$ 排队延迟
  - 寻找从当前节点$u$到$t_i$的路径
    - 记录节点选择顺序（加入栈$c_p$中）
    - 遍历图$\mathbb{H}$中的每一个节点$v$：
      - 若边$e(u,v)$是可通过的 ($M[u][v]>0$) 且满足延迟约束 ($L_t+E[u][v] < \beta_i$)
        - 如果该边是有向边($M[u][v]=2$)，则将边$e(u,v)$不再是可通过的边($M[u][v]=0$)
        - 如果该边是无向边($M[u][v]=1$)，则将该边$e(u,v)$和$e(v,u)$都不再可通($M[u][v]=0, M[v][u]=0$)
      - 更新当前节点 $u=v$
    - 更新总延迟
  - 递归调用，直到$u=t_i$，返回$c_p$并加入$P_t^{\Lambda}$
![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-algo-cdfsa.png)

### 部署VNF

部署时应满足

- VNF需顺序部署
- 被部署的服务器满足计算资源约束
- 尽可能重用已运行的VNF

#### 构建矩阵

对于一个候选路径$p \in P_t^{\Lambda}$，

- 其中边个数为$k$，则服务器个数为$k+1$
- 其服务器组成为$p= \{sp_0,sp_1,sp_2,\cdots,sp_k\}$

**$S_i$-$p$关系矩阵 $A$**

- 矩阵形状为$|S_i| \times (k+1)$
- $A[l][j]$表示VNF $s_l \in S_i$能否重用服务器$sp_j$，需满足两个约束：
  - 该服务器已运行着此类VNF
  - 该服务器对此类VNF的剩余处理能力满足约束
  - $A[l][j]=\left\{\begin{array}{ll}
1 & \text { if } W_{\left(d_{j}\right),\left(n_{l}\right)}-r_{i} \geq 0 \\
0 & \text { otherwise. }
\end{array}\right.$

示例如下

$$\left.A= ( \begin{array}{cccccccc}
& d_{0} & d_{1} & d_{2} & d_{3} & d_{2} & d_{4} & d_{4} \\
s_{0} & 0 & 1 & 0 & 1 & 0 & 1 & 0 \\
s_{1} & 0 & 0 & 1 & 0 & 1 & 0 & 1 \\
s_{2} & 0 & 1 & 0 & 1 & 0 & 1 & 0 \\
s_{3} & 1 & 1 & 1 & 0 & 1 & 0 & 0
\end{array}\right)$$

#### PGA

A Path-based Greedy Algorithm

每个VNF有两种服务器可部署

- type-R：可复用的，已运行该类VNF的服务器
- type-V：可新开的，有足够资源新开VNF的服务器

对于每个被部署的VNF $s_l$ 有四种情况

- 情况1：先找到type-R，即最优方案
- 情况2：先找到type-V再找到type-R
- 情况3：仅找的type-V
- 情况4：两种都没找到，部署失败，拒绝该SFC

可以发现，对于每个VNF部署，最多只需尝试2种可选服务器

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-algo-pga.png)

#### 最优方案

对于每个候选路径$p \in P_i^{\Lambda}$，利用上述算法可以得到了其局部最优方案。之后，在这些候选路径中，选择资源消耗最少的方案，即为最优方案。

## 实验评估

### 设计实验

#### 底层网络

##### 网络拓扑

用不同大小的网络拓扑模拟不同的网络规模

| 名称 | 节点数 | 边数 |
| :---:| --- | --- |
Bellsouth | 51  | 66
Cogentco | 197 | 245
Kdl  | 754 | 899

##### 服务器节点

为了模拟运行中的网络状态，每个服务器

- 当前剩余可用资源为$[0,200]$单元
- 预先随机放置$[0,8]$种类型的VNF（共20种）

##### 物理链路

每条链路的带宽资源为$[0,1000]$ Mbps

#### 服务功能链

SFC依次到达系统，为每个SFC从底层网络中随机挑选一组源节点（APs）和目标节点

- 每个AP处理能力为$[100,200]$
- 每个VNF所需计算资源$[20,50]$节点
- 流速为$[30，60]$ Mbps
- 生存周期为$[30,80]$ ms

**每一次实验重复20次来消除偶然性**

### 对比算法

- Shortest Path Heuristic + First Fit Assignment (SHP+FF)  
- Constrained Depth-First Search Algorithm + First Fit Assignment (CDFSA + FF)  
- Shortest Path Heuristic + Path-based Greedy Algorithm
(SHP+PGA)  

### 评估指标

#### 运行时间

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-experiment-execution-time.png)

#### 资源消耗

##### SFC长度+流速影响

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-experiment-sfc-length-and-flow-rate.png)

##### 流速

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-experiment-flow-rate.png)

##### SFC长度

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-experiment-sfc-length.png)

#### 平均延迟

![ ](/resource/images/paper/paper-nfv-vnfp-latency-ne-experiment-average-latency.png)

## 总结思考

 - 场景新颖：在网络边缘场景，且考虑了延迟
 - 矩阵重构：提出了混合图来对链路访问进行限制
 - 先边后点：两阶段解决方案，先选路径后进行VNF部署
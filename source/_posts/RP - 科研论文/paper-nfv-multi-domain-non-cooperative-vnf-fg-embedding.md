---
title: 【论文笔记】Multi-domain Non-cooperative VNF-FG Embedding - A DRL Approach
author: Gemini向光性
date: 2020-11-29
categories: 
  - RP - 科研论文
tags: 
  - Paper
  - NFV
  - DRL
mathjax: true
# top: true
---

本文研究了多域非合作场景下的VNF放置问题，提出了一种深度强化学习DDPG算法与基于成本的首次拟合算法（CCF）相结合的方法来实现最大化VNF部署个数和最小化部署成本的目标。

## 论文简介

**论文名称**：Multi-domain Non-cooperative VNF-FG embedding: A deep reinforcement learning approach  
**论文作者**：Quang Tran Anh Pham, Abbas Bradai, Kamal Deep Singh, Yassine Hadjadj-Aoul  
**发表期刊**：INFOCOM-2019 (CCF-A)  
**研究方向**：NFV 网络功能虚拟化  
**关键技术**：多域非合作，虚拟网络功能嵌入, 深度强化学习  
**主要创新**：多域非合作场景；多通道的VNF-FG请求矩阵表示；基于Cost的First Fit算法  
[下载论文](https://hal.archives-ouvertes.fr/hal-02088819/file/MultiDomain_VNF_FG_embedding__A_Deep_reinforcement_learning_approach-authors%20version.pdf)

<!-- more -->

## 问题定义

### 缩写释义

| 缩写 | 描述 | 全称 |
| :---:| --- | --- |
NFV | 网络功能虚拟化 | Network Function Virtualization
VNF-FG | 虚拟网络功能转发图 | Virtual Network Function - Forwarding Graph
VNF | 虚拟网络功能 | Virtual Network Function
VL | 虚拟链路 | Virtual Link
QoS | 服务质量 | Quality of Service
DDPG | | deep deterministic policy gradient

### VNF-FG

每个VNF-FG由若干由VL连接起来的VNF组成

- $\mathcal{N'}$表示VNF的集合，$|\mathcal{N'}|$为VNF个数
  - 对于VNF，考虑$K_{VNF}$种资源，如CPU、RAM、Storage
  - $h_{n',k}$表示VNF $n'$对资源$k$的请求量
  - $h_{n'} = [h_{n',0}, ..., h_{n',K_{VNF-1}}]$表示VNF $n'$对各种资源的请求量
- $\mathcal{L'}$表示VL的集合，$|\mathcal{L'}|$为VNF个数
  - 对于VL，考虑$K_{VL}$种资源，如bandwidth、latency、packet loss
  - $h_{l',k}$表示VNF $l'$对资源$k$的请求量
  - $h_{l'} = [h_{l',0}, ..., h_{l',K_{VNF-1}}]$表示VNF $l'$对各种资源的请求量

### 问题约束

- 一个VNF可以被成功部署在一个有足够的资源的主机上，且只能部署在一个主机上
$$\sum_{n^{\prime}} \phi_{n}^{n^{\prime}} h_{n^{\prime}, k} \leq r_{n, k}, \forall n, k$$
$$\sum_{n} \phi_{n}^{n^{\prime}} \leq 1, \forall n^{\prime}$$

- 一个虚拟链路可以被部署在拥有足够资源且满足QoS要求的底层链路上
  
$$\sum_{l^{\prime}} \phi_{l}^{l^{\prime}} h_{l^{\prime}, b w} \leq r_{l, b w}, \forall l$$
$$h_{l^{\prime}, \text {delay}} \leq D\left(\phi^{\prime^{\prime}}\right)$$
$$h_{l^{\prime}, \text {loss}} \leq R\left(\phi^{l^{\prime}}\right)$$

### VNF-FG 请求

一个三维矩阵 $\left|\mathcal{N}^{\prime}\right| \times\left|\mathcal{N}^{\prime}\right| \times\left(2 \times K_{\mathrm{VNF}}+K_{\mathrm{VL}}\right)$，可以看做$\left(2 \times K_{\mathrm{VNF}}+K_{\mathrm{VL}}\right) \text { -channel of }\left|\mathcal{N}^{\prime}\right| \times\left|\mathcal{N}^{\prime}\right|$，即$\left(2 \times K_{\mathrm{VNF}}+K_{\mathrm{VL}}\right)$个通道的$\left|\mathcal{N}^{\prime}\right| \times\left|\mathcal{N}^{\prime}\right|$的矩阵表示。

- $K_{VL}$ 描述了虚拟链路的资源请求
- $2 \times K_{VNF}$ 分别描述了源VNF和目的VNF的资源请求

### 资源价格

VNF $n'$放置在底层节点$n$上时，其资源$m$的价格表示为$c^m_{n,n^{\prime}}$，则VNF $n'$的价格向量可表示为：$\mathbf{c}_{n^{\prime}}=\left[\mathbf{c}_{0, n^{\prime}}, \mathbf{c}_{1, n^{\prime}}, \ldots, \mathbf{c}_{|\mathcal{N}|-1, n^{\prime}}\right]$，其中$c_{i,n^{\prime}}=[\mathbf{c}_{0, n^{\prime}}^{0},\cdots,\mathbf{c}_{0, n^{\prime}}^{k_{VNF}-1}]$。因此，需要用$\left|\mathcal{N}^{\prime}\right| \times\left|\mathcal{N}^{\prime}\right| \times K_{VNF}$矩阵来表示。

VL $l'$带宽资源的代价表示为$c_{i,l'}$，则VL $l'$的价格向量可表示为：$\mathbf{c}_{l^{\prime}}=\left[\mathbf{c}_{0, l^{\prime}}, \mathbf{c}_{1, l^{\prime}}, \ldots, \mathbf{c}_{|\mathcal{L}|-1, l^{\prime}}\right]$，需要用$\left|\mathcal{L}^{\prime}\right| \times\left|\mathcal{L}^{\prime}\right|$来表示。

这些价格由每个与决定，它们会被发送到中心客户。

## 算法模型

### 多域非合作架构

在该文中，环境是由多个非合作的域和一个中心客户（client）组成的。这些域有这些特征：

1. 每个域都没有其他域的拓扑结构和资源信息
2. 每个域不与其他域进行通信，
3. 每个域单独做出决策将发送至中心客户，由中心客户根据价格进行选择

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-fg-embedding-1.png)

大致的决策流程如下：

1. 中心代理将VNF-FG请求处理为一个三维向量，即`state`，然后发送给每个域
2. 每个域使用actor网络根据`state`计算出`action`。每个域的动作$A_i$是向中心客户提出的价格，如$c_{n, n^{\prime}}^{k}, \forall n \in \mathcal{N}_{i}$和$c_{l, l^{\prime}}, \forall l \in \mathcal{L}_{i}$分别代表节点和链路成本。
3. 中心代理根据这些信息作出最终决策，每个域根据决策来部署VNF和VL，最后得到相应奖励

### 深度强化学习 DDPG

#### 马尔科夫决策 MDP

- 环境 Environment：多个域和一个中心客户
- 状态 State：经过处理的VNF-FG请求，是一个三维矩阵。
- 动作 Action：行为就像是对出售其资源的域名的竞价，它包括对节点资源和链路资源的报价。
- 奖励 Reward：根据部署质量和资源报价，中心代理返回给每个域相应奖励
$$r_{i}=\sum_{n^{\prime} \in \mathcal{N}^{\prime}} \sum_{n \in \mathcal{N}_{i}} \sum_{k \in K_{c}} \omega_{n}^{n^{\prime}} c_{n, n^{\prime}}^{k} h_{n^{\prime}, k}+\sum_{l \in \mathcal{L}_{i}} \sum_{l^{\prime} \in \mathcal{L}^{\prime}} \omega_{l}^{l^{\prime}} c_{l, l^{\prime}} h_{l^{\prime}, b w}$$
式中，$w_n^{n'}$和$w_n^{n'}$表示放置成功二进制位，当且仅当节点和链路被成功放置且满足服务质量时才为1。

#### 代理架构 Agent

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-fg-embedding-2.png)

DDPG由两个神经网络组成

- Actor network: 学习策略。根据当前策略和输入的$s_t$来生成动作，要注意的是，动作被添加了噪声$N$来使agent更好地探索环境。
- Critic network: 学习Q值。评估一个动作的优劣，来使actor学习到更好地策略。

actor和critic神经网络由多种层组成来提取state中的信息，特别地，critic额外使用了卷积层来提取动作的特征：

- 3层卷积层 convolutional layers
  - 卷积层$i$的过滤器和卷积核的大小为$C_i$和$(F_i, F_i)$
  - 卷积层的输出反映了虚拟链路及其属性间的相互影响
- 平均池化层 average pool layers
  - 减少参数数量和防止过拟合
- 全连接层 Fully Connected layers
  - 将这些反映state信息相互影响的中间输出映射为动作

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-fg-embedding-3.png)

收到每个域返回的动作（资源竞价），中心客户会做出最终的决定并执行，然后每个域会依据提供的资源收到相应的奖励，最后DRL代理会计算loss来更新参数。

对于域$i$，其目标是找到一个从VNF-FG请求到本地动作的映射策略：$\mu_i:S \rightarrow A_i$来最大化获得奖励。

### 决策算法 CFF

在收集了来自不同域$A_i$的价格后，中心客户必须做出决定来部署其VNF-FG。虽然很难保证一条路径能够满足虚拟链路所需的QoS，但是可以保证VNFs的资源需求。因此，在部署VNF时，决策必须考虑底层节点的容量以及部署的成本。

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-fg-embedding-4.png)

该文使用了将一种资源分配算法First-Fit算法改进为基于成本的First-Fit（CFF）算法来进行从VNF到底层节点的决策生成：

- 输入：每个域的动作$A_i$
- 输出：VNF的映射集合$\phi_n^{n'}$
- 初始化 $\phi_n^{n'}=0, \forall n, n'$, $A_* \leftarrow [A_1, \cdots, A_D]$
- 将全局动作A_*分割为与VNF相关的成本$A_{*,vnf}$和与VL相关的成本$A_{*,vl}$
- 对于每个VNF $i$：
  - 实际部署成本等于VNF $i$的成本与其资源请求量$h_i$的点乘：$P = A_{*,vnf}[i]=c_i \cdot h_i$，由此可得到每个底层节点部署当前VNF的成本
  - 按照部署成本$P$进行升序排列
  - 对于排列后的域序列中的单个域$j$：
    - 中心代理具有$j$的域$D(j)$发送请求
    - 如果域$D(j)接受了该请求，那么
      - $\phi_n^{n'}=1$，域$D(j)$部署j到节点i上

映射VNF策略的目标是最大化部署个数和最小化部署成本。映射虚拟链路$l'$时，根据$A_{*,vl}[l']$作为链路权重，使用Dijkstra算法来寻找最小成本的链路。

## 性能评估

### 设计实验

#### 网络拓扑设置

- BtEurope网络拓扑：24个节点和37个全双工边
- 链路资源容量：20 Mbps, 30 Mbps, 50 Mbps, 100 Mbps随机分配
- 节点资源容量：$(0.3, 2.0)$ 随机分配
- 使用延迟和丢包率来评价网络虚拟链路的部署质量

#### VNF-FG配置

- 3到6个VNF组成，VNF连接率为0.5
- VNF资源请求量被进行归一化
- VL请求：$(1,30)$ Mbps随机分配，延迟为1~100毫秒，延迟率为0%~0.5%

#### 神经网络参数

| 符号 | 含义 | 取值 |
| :---:| --- | --- |
\ | 优化器 |Adam
\ | actor学习率 | $10^{-4}$
\ | critic学习率 | $10^{-3}$
$\gamma$ | 折扣因子 | 0.99
$\tau$ | target网络更新系数 | =0.001
\ | batch size | 32
\ | 全连接层unit个数 | 300
\ | 卷积层卷积核 | $(4,4),(2,2),(5,5)$

### 对比算法

- CFF-SD  
  single domain with CFF，单域CCF算法，是非竞争的
- CFF-3D  
  three domain with CFF，三域使用CCF算法
- SA
  simulated annealing algorithm，模拟退火算法
- SA-CFF  
  simulated annealing algorithm with CFF，模拟退火和CCF算法结合

### 评估指标

#### 平均奖励和单位价格

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-exp-reward.png)

#### VNF和VL部署成功率

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-exp-deploy.png)

#### 部署成功率对比

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-exp-comparison.png)

#### 多域部署情况

每个域平均资源情况

- 域1有最少的CPU和RAM资源
- 域3有最少的存储资源

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-exp-resource.png)

每一个域实际部署情况

- 域2部署的VNF和VL都是最多的
- DRL会考虑负载均衡来进行放置决策

![ ](/resource/images/paper/paper-nfv-multi-domain-non-cooperative-vnf-exp-mean-domain-deploy.png)

## 总结思考

- 场景特殊：多域非合作场景下的VNF放置问题
- 资源种类多：对于VNF和VL均考虑多种资源约束
- 矩阵构造：将VNF-FG请求处理为一个包含VNF和VL请求信息的3维矩阵
- 深度强化学习：使用了DDPG的DRL方法来训练神经网络
- 改进启发式算法：改进了First Fit算法来达到最小化部署成本的目标

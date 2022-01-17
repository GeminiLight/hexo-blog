---
title: 【论文精读】Deep Reinforcement Learning for Seq2Seq Models
author: Gemini向光性
date: 2020-8-28
categories: 
  - RP - 科研论文
tags: 
  - 论文精读
  - 强化学习
  - Seq2Seq
mathjax: true
# top: true
---

本篇论文将强化学习应用于Seq2Seq模型，来优化Seq2Seq模型的效果

<!-- more -->

## 论文简介

**论文名称**：Deep Reinforcement Learning for Sequence-to-Sequence Models
**论文作者**：Yaser Keneshloo, Tian Shi, Naren Ramakrishnan, Chandan K. Reddy, Senior Member , IEEE  
**发表期刊**：TNNLS-2019 (CCF-B)  
**研究方向**：Sequence-to-Sequence Model
**关键技术**：Deep learning; reinforcement learning; sequence to sequence learning; Q-learning; actor-critic methods;
**主要创新**：将强化学习与Seq2Seq模型相结合，来优化Seq2Seq模型的效果
[下载论文](https://arxiv.org/pdf/1805.09461.pdf)  
[查看代码](https://github.com/yaserkl/RLSeq2Seq)

## Seq2Seq模型

### 简单的Seq2Seq模型

Seq2Seq模型在机器翻译、新闻标题生成、文本提取、声音识别和图像字幕等领域有着广泛的应用。

$$\begin{array}{l}
h_{t^{\prime}}=\Phi_{\theta}\left(x_{t^{\prime}}, h_{t}\right) \\
s_{t^{\prime}}=\Phi_{\theta}\left(y_{t}, s_{t} / h_{T_{e}}, c_{t}\right) \\
\hat{y}_{t^{\prime}} \sim \pi_{\theta}\left(y \mid \hat{y}_{t}, s_{t^{\prime}}\right)
\end{array}$$

$$\begin{array}{l}
s_{t^{\prime}}=\sigma\left(W_{1} y_{t}+W_{2} s_{t}+W_{3} c_{t}\right) \\
o_{t^{\prime}}=\operatorname{softmax}\left(W_{4} s_{t^{\prime}}+W_{5} c_{t}\right)
\end{array}$$

### Seq2Seq2模型的问题

## 问题定义

### 专业词汇

| 缩写 | 描述 | 全名 |
| :---:| --- | --- |
VNE | 虚拟网络嵌入 | Virtual Network Embedding
VNR | 虚拟网络请求 | Virtual Network Request
RL  | 强化学习     | Reinforcement Learning
GCN | 图卷积神经网络 | Graph Convolutional Network
A3C | | Asynchronous Advantage Actor-Critic

### VNE

## 算法模型

## 性能评估

### 设计实验

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

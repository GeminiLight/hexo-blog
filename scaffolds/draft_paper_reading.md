---
title: 【论文精读】Automatic Virtual Network Embedding - A DRL Approach with GCN
author: Gemini向光性
date: 2020-7-13
categories: 
  - RP - 科研论文
tags: 
  - 论文精读
  - VNE
  - 强化学习
  - GNN
  - GCN
mathjax: true
# top: true
---

本篇论文

<!-- more -->

## 论文简介

**论文名称**：
**论文作者**：
**发表期刊**：JSAC-2020 (CCF-A)  
**研究方向**： 
**关键技术**：
**主要创新**： 
[下载论文](https://ore.exeter.ac.uk/repository/bitstream/handle/10871/40799/Yan-JSAC-2020.pdf?sequence=1&isAllowed=y)

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

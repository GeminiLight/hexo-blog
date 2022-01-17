---
title: 【论文笔记】VNF placement optimization with DLR
author: Gemini向光性
date: 2020-8-20 23:42:32
categories: 
  - RP - 科研论文
tags: 
  - Paper
  - NFV
  - DRL
mathjax: true
---

本篇论文对资源消耗成本最小化的强化学习Policy Gradient算法，结合Seq2Seq模型，提出了一种虚拟网络服务放置的优化算法。

## 论文简介

**论文名称**：Virtual Network Function placement optimization with Deep Reinforcement Learning  
**论文作者**：Ruben Solozabal, Josu Ceberio, Aitor Sanchoyerto, Luis Zabala, Bego Blanco, Fidel Liberal  
**发表期刊**：JSAC-2020 (CCF-A)  
**研究方向**：NFV 网络功能虚拟化  
**关键技术**：NFV 网络功能虚拟化, RL 强化学习, Seq2Seq  
**主要创新**：以最小化资源消耗为目标，并用策略梯度算法结合Seq2Seq来搭建模型  
[论文地址](https://ieeexplore.ieee.org/document/8945291)

<!-- more -->

## 问题定义

### 专业词汇

| 缩写 | 描述 | 全名 |
| :---:| --- | --- |
NFV | 网络功能虚拟化 | Network Function Virtualization
VNF | 虚拟网络请求 | Virtual Network Functions
NS  | 网络服务     | Network Service
VNF-FGE | VNF正向图嵌入问题 | VNF Forward Graph Embedding problem

### VNF-FGE

![ ](/resource/images/rp-vnf-placement-optimization-with-dlr-1.webp)

![ ](/resource/images/rp-vnf-placement-optimization-with-dlr-2.webp)

对于一组网络服务，

- 它必须被最优地被放置在一组主机服务器上，即 $h \in H$
- 满足主机服务器在计算、存储链路容量等方面的现在，即 $s \in S$

问题目的：最小化对底层资源的消耗

$H = \{ h_1, h_2, \dots, h_n \}$  主机服务器  
$V$ 可用的VNF  
$m \in \{1, \dots, M\}$  一系列VNF组成的网络服务
$s \in \{f_1, f_2, \dots, f_m \}$  ，且$f \in M$  一条服务链  
$S$ 所有的服务链组合

$x \in \{0, 1\}^{m\times n}$

- $x_{fh}$ 表示功能 $f \in V$ 是否被放置在主机 $h \in H$ 中（1放置，0未放置）

动作搜索路径：$\Omega = \{0,1\}^{m \times n}$ s.t. $\left.\sum_{h} x_{f h}=1 \forall f \in s\right\}$

- 对于一条服务链，它只能放置在一个主机一次

辅助变量

- $y_h \in \{0,1\}$：服务器激活变量。1代表服务器正在执行VNF，0反之
- $g_i \in \{0,1\}$：链路激活变量。1代表链路正在承载流量，0反之

功耗相关

- 服务器主机功耗 $W_h^{cpu}$
  - 激活运行时（$y_h = 1$）的最低功耗为 $W_h^{min}$
  - 功耗随着VNF的CPU需求总和而增加（线性增长）
- 链路消耗 $W_{net}$
  - 带宽利用量 x 单位成本

可用资源 $r \in R$

![ ](/resource/images/rp-vnf-placement-optimization-with-dlr-3.webp)

## 算法模型

![ ](/resource/images/rp-vnf-placement-optimization-with-dlr-4.webp)

![ ](/resource/images/rp-vnf-placement-optimization-with-dlr-5.webp)

## 性能评估

### 设计实验

### 对比算法

### 评估指标

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

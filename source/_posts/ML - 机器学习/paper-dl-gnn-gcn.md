---
title: 【论文笔记】GNN之GCN：Semi-Supervised Classification with Graph Convolutional Networks
author: Gemini向光性
date: 2020-7-27
categories: 
  - RP - 科研论文
tags:
  - DL
  - GNN
---

本文提出了一种基于卷积操作的图神经网络GCN，它将卷积操作适应于图结构，同时提取图结构和节点的特征信息，并在在半监督学习中获得了较好的效果提升。

**论文名称**：Semi-Supervised Classification with Graph Convolutional Networks  
**论文作者**：Thomas N. Kipf, Max Welling  
**发表期刊**：ICLR-2017 (THU-A)  
**研究方向**：GNN 图神经网络  
**关键技术**：Graph Convolution  
**主要创新**：将多头注意力机制应用于图神经网络，来提升特征提取效果。  
[下载论文](https://arxiv.org/abs/1609.02907) |
[查看源码](https://github.com/tkipf/gcn?utm_source=catalyzex.com)

<!-- more -->

## 论文简介

### 缩写释义

| 缩写 | 描述 | 全称 |
| :---:| --- | --- |
GNN | 图神经网络 | Graph Neural Network
GCN | 图卷积网络 | Graph Convolutional Network

### 研究背景

- 

### 主要贡献

- 提出了一种简单、有效且可以直接在图上进行的卷积操作的神经网络
- 证明了GCN可以快速的和可伸缩处理半监督节点分类问题

## 模型算法

### 多层GCN传播规则

多层的 Graph Convolutional Network (GCN) 传播规则如下

$$H^{(l+1)}=\sigma\left(\tilde{D}^{-\frac{1}{2}} \tilde{A} \tilde{D}^{-\frac{1}{2}} H^{(l)} W^{(l)}\right)$$

式中，

- $\tilde{A}=A+I_{N}$是无向图$\mathcal{G}$的邻接矩阵与自连接的单位矩阵的加和
- $\tilde{D}_{i i}=\sum_{j} \tilde{A}_{i j}$
- $W^{(l)}$是分层可训练的权重矩阵
- $\sigma (·)$是一个激活函数
- $H^{(l)} \in \mathbb{R}^{N \times D}$是第$l$层的激活矩阵，且$H^{(l)}=X$

### 基于谱方法的图卷积

图的谱卷积操作被定义为信号$x \in \mathbb{R}^N$与傅里叶域滤波器$g_{\theta}=\operatorname{diag}(\theta)$的乘积：

式中，$U$是归一化图拉普拉斯特征向量的矩阵，满足

$$L=I_{N}-D^{-\frac{1}{2}} A D^{-\frac{1}{2}}=U \Lambda U^{\top}$$

其特征值$\Lambda$的对角矩阵和$U^{\top} x$是$x$的图傅里叶变换。我们可以理解$g_{\theta}$为$L$特征值的函数，如$g_{\theta}(\Lambda)$。

但大型图的特征分解是高计算需求的。为了避免这个问题，$g_{\theta}(\Lambda)$可以通过切比雪夫多项式$T_k(x)$的截断展开很好地逼近到第$K^{th}$阶：

$$g_{\theta} \star x=U g_{\theta} U^{\top} x$$

式中，$\tilde{\Lambda}=\frac{2}{\lambda_{\max }} \Lambda-I_{N}$，$\lambda_{\max }$表示L的最大特征值。$\theta' \in \mathbb{R}^K$是切比雪夫系数的向量表示。切比雪夫多项式递归定义为$T_{k}(x) = 2 x T_{k-1}(x)-T_{k-2}(x)$, $T_{0}(x)=1, T_1(x)=x$。

因此，信号$x$和滤波$g_{\theta^{\prime}}$的卷积操作可以定义为
$$g_{\theta^{\prime}}(\Lambda) \approx \sum_{k=0}^{K} \theta_{k}^{\prime} T_{k}(\tilde{\Lambda})$$
式中，$\tilde{L}=\frac{2}{\lambda_{\max }} L-I_{N}$，因为$\left(U \Lambda U^{\top}\right)^{k}=U \Lambda^{k} U^{\top}$。注意，这个表达式现在是K-local，因为它是Laplacian中的第K阶多项式，即它只依赖于距离中心节点（第K阶邻域）最大K步的节点。


$$g_{\theta^{\prime}} \star x \approx \sum_{k=0}^{K} \theta_{k}^{\prime} T_{k}(\tilde{L}) x$$

## 模型改进

## 性能评估

### 数据集

### 评估指标

## 总结思考

- 

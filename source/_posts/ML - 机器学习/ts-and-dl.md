---
title: 【ML】时序预测的深度学习方法总结
author: Gemini向光性
date: 2021-06-30
categories: ML - 机器学习
tags:
   - TS
   - DL

mathjax: true
---

![ ](/resource/images/report/ts-and-dl/1.jpg)

总结一些自己所阅读DL-based时序预测算法的文献，包括了一些基于CNN、RNN、Attention、Transformer、GNN、GAN的算法。

为了对抗拖延症打算先放图占个坑，后续会逐一补充文字说明。在文字总结时，自己也会继续扩充相关内容和进行勘误，并分享slide版本。

如您发现错误，欢迎指正~

<!-- more -->

![ ](/resource/images/report/ts-and-dl/2.jpg)

## 时序预测简介

![ ](/resource/images/report/ts-and-dl/3.jpg)

![ ](/resource/images/report/ts-and-dl/4.jpg)

## TS经典方法

![ ](/resource/images/report/ts-and-dl/5.jpg)

![ ](/resource/images/report/ts-and-dl/6.jpg)

## DL方法汇总

### 基础模型：Canonical与Seq2Seq

#### MLP

![ ](/resource/images/report/ts-and-dl/7.jpg)

#### CNN

![ ](/resource/images/report/ts-and-dl/8.jpg)

#### RNN

![ ](/resource/images/report/ts-and-dl/9.jpg)

![ ](/resource/images/report/ts-and-dl/10.jpg)

![ ](/resource/images/report/ts-and-dl/11.jpg)

![ ](/resource/images/report/ts-and-dl/12.jpg)

#### Seq2Seq

![ ](/resource/images/report/ts-and-dl/13.jpg)

![ ](/resource/images/report/ts-and-dl/14.jpg)

![ ](/resource/images/report/ts-and-dl/15.jpg)

### Attention-based 方法

![ ](/resource/images/report/ts-and-dl/16.jpg)

#### LSTNet

![ ](/resource/images/report/ts-and-dl/17.jpg)

![ ](/resource/images/report/ts-and-dl/18.jpg)

![ ](/resource/images/report/ts-and-dl/19.jpg)

![ ](/resource/images/report/ts-and-dl/20.jpg)

![ ](/resource/images/report/ts-and-dl/21.jpg)

![ ](/resource/images/report/ts-and-dl/22.jpg)

#### MTNet

![ ](/resource/images/report/ts-and-dl/23.jpg)

![ ](/resource/images/report/ts-and-dl/24.jpg)

![ ](/resource/images/report/ts-and-dl/25.jpg)

![ ](/resource/images/report/ts-and-dl/26.jpg)

![ ](/resource/images/report/ts-and-dl/27.jpg)

![ ](/resource/images/report/ts-and-dl/28.jpg)

![ ](/resource/images/report/ts-and-dl/29.jpg)

#### TPA-LSTM

![ ](/resource/images/report/ts-and-dl/30.jpg)

### Transformer

![ ](/resource/images/report/ts-and-dl/31.jpg)

![ ](/resource/images/report/ts-and-dl/32.jpg)

![ ](/resource/images/report/ts-and-dl/33.jpg)

#### N-BEATS

![ ](/resource/images/report/ts-and-dl/34.jpg)

#### Informer

![ ](/resource/images/report/ts-and-dl/35.jpg)

![ ](/resource/images/report/ts-and-dl/36.jpg)

![ ](/resource/images/report/ts-and-dl/37.jpg)

### GNN-based 方法

#### MTGNN

![ ](/resource/images/report/ts-and-dl/38.jpg)

![ ](/resource/images/report/ts-and-dl/39.jpg)

![ ](/resource/images/report/ts-and-dl/40.jpg)

![ ](/resource/images/report/ts-and-dl/41.jpg)

![ ](/resource/images/report/ts-and-dl/42.jpg)

![ ](/resource/images/report/ts-and-dl/43.jpg)

#### MTAD-GAT

![ ](/resource/images/report/ts-and-dl/44.jpg)

#### GDN

![ ](/resource/images/report/ts-and-dl/45.jpg)

![ ](/resource/images/report/ts-and-dl/46.jpg)

![ ](/resource/images/report/ts-and-dl/47.jpg)

![ ](/resource/images/report/ts-and-dl/48.jpg)

![ ](/resource/images/report/ts-and-dl/49.jpg)

![ ](/resource/images/report/ts-and-dl/50.jpg)

![ ](/resource/images/report/ts-and-dl/51.jpg)

#### Summary

![ ](/resource/images/report/ts-and-dl/52.jpg)

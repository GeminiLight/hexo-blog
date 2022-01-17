---
title: 【学术讲座】CCF 2020 中国软件大会
author: Gemini向光性
date: 2020-11-22 09:14:00
categories:
  - RP - 科研论文
tags:
  - Lecture
  - Research
  - AI
---

CCF 2020 中国软件大会上郑志明、何积丰、等院士的讲座分享。

- 郑志明院士：精准智能——面向动态复杂对象的AI
- 何积丰院士：思维机器与强人工智能

<!-- more -->

## 郑志明院士：精准智能——面向动态复杂对象的AI

### 近现代科学

#### 近代科学

时期：17-19世纪
标志：牛顿、线性化

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-zzm-1.jpg)

后牛顿时代
- 研究：具有非线性、动态、随机特征的复杂动态系统
- 困难：三维以上的动态系统几乎都是复杂系统

线性与非线性

- 复杂度：非线性明显高于线性
- 收敛性：非线性收敛点可能是一片

#### 现代科学

时期：20世纪初以来

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-zzm-2.jpg)

发展现状

- 复杂物理或自然系统→复杂数据系统
- 传统复杂物理自然系统的数字化表征→科学进入大数据时代

### 人工智能

#### AI介入数据的科学分析

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-zzm-3.jpg)

#### 数学角度下当前的AI

当前的人工智能主要是**逻辑或统计+动态线性学习方法**来**逼近非线性动态系统的不变集**

通常所说大数据复杂，数据特征复杂仅是一方面，而导致系统复杂的关键要素是**非线性随机关联关系**

如果这样的方法可行，牛顿科学就终结了现代科学

#### 新的尝试

DARPA率先在基础研究层面布局，探索**内嵌物理、数学和先验知识**的人工智能原型，准确性、稳定性、普适性等方面的性能关破

### 精准智能

| 人工智能 | → | 精准智能 |
| - | - | - |
| 大数据 <br> 统计特征 | 探索内嵌先天科学结构的数据表述范式<br>基于非线性关联关系的数据表征 | 科学数据 <br> 数理表征 |
| 统计+线性 | | 近似科学数据场 |
| 基于调参学习 | 深索基于相变和分支突变系统学习<br>理论演化结构理论、小概率（小样本）事件学习方法 | 基于系统行为学习 |

- 爆炸式渗流——**内嵌渗流系统的分支性态发现和突变学习方法**，用于精准的发现信息全局爆发节点，提出抑制和控制策略
  ![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-zzm-4.jpg)
- AMS-02——**内嵌扩散机理的复杂系统耦合、解耦和演化学习方法**，在P级数据中快速准确发现含有暗物质信息的K到M级数据，误差约万分之一。
  ![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-zzm-5.jpg)

## 何积丰院士：思维机器与强人工智能

### Causal Derivation based AI

> "The knowledge gained from data-centered machine learning is weak in empirical intepretation and may be completely opposite to the original knowledge."  
> "The next generation of AI will focus on causal derivation and be centered on science rather than data."  
> ——Judea Pearl

现阶段的AI：

- 数据 → 模型 $f_k(x)$ → 结果 $y_k$
- 结果的不确定性，即结果不一定是正确的
- 缺乏可解释性不可怕，人脑也是，重要的是要可与环境对话！

#### Role of a casual Reasoning Model

Give machines the ability

- to reflect on their mistakes
- to pinpoint weakness in their software
- to converse naturally with humans about their own choices and intentions

#### Language of Thought

Use logic tools to model thinking

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-hjf-2.jpg)

#### Complex Thinking Combination

Thinking is the process of processing and digesting knowledge and generating new knowledge, the in-depth understanding and research on the combination of complex thinking．It is the key to break through the current data-based artificial intelligence
mechanisms and models.

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-hjf-3.jpg)

### Calculus of Causation

#### Language of Knowledge

A diagrammatic language of causal diagrams is used to
express what we know. They are simply dot and_arrow
pictures where

- the dots represent quantities of interests, and
- the arrows represent known or suspected relationship between those quantities

They are subject to the scrutiny of data, and can be falsified.

> Example: Consider the chain A→B→C. If the observed data do not show a and c to be independent, conditional on b, then we can conclude that the chain model needs to be discarded or repaired.

The graphic properties dictate which causal models can be distinguished by data, and which will forever remain indistinguishable.

> Example: no data can distinguish A←B→C from A→B→C, because with C listening to B only, the two imply the same independence conditions.

Each arrow can be thought of as a statement about the outcome of a hypothesis experiment.

1. An arrow from a to C means that if we would wiggle only A, then it is expected to see a change in the probability of C
2. A missing arrow from a to C means in the same experiment we would not see any change in C, once we held constant the parents of C

！模型的逻辑适应性

#### Language of Queries

To express the questions we want answers to

1. Association
   - Activity:　seeing, observing
   - Questions: How would seeing X change my belief of Y
   - Example: What does symptom tell me about disease
2. Intervention
   - Activity: doing, intervening
   - Questions: How can I make Y happen
   - Example: What if we ban smoking
3. Counterfactuals
   - Activity: imaging, retrospection, understanding
   - Questions: Was it X caused Y? What if I had acted differently
   - Example: What if I had not smoked for the last two years

### Big Data and Causal Model

The amount of raw data has grown at a staggering rate in recent years
The rise of huge databases in science

数据为中心的AI：

1. 观测现有数据
2. 动态生成衍生数据来探索更多场景
3. 人机互联，环境交互

#### Symbiosis between Big Data and Causal Inference

- Background  
Thanks to Big Data, we can access an enormous number of studio, conducted in different location and under different condition
- Questions  
Can we combine the results of these studio and translate them to new population. i.e there a complete criterion for deciding when results are transportable and when they are　not

#### Inference Machines

![ ](/resource/images/lecture/lecture-ccf-chinasoft-2020-hjf-1.jpg)

### Strong AI

1. A machine can reflect on its actions and learn from past mistake.
2. It should be able to understand the statement"i should have acted differently.
3. Intent is a very important part of personal decision making. The ability to conceive of one's own intent and then use it as a pieces of evidence in casual reasoning is a level of self-awareness.
4. The discussion of 3 leads to a major issue for strong ai free will
5. The ability to reason about one's own beliefs, intents, and desires has been a major challenge to ai and defines the notion of "agency".  
  The software package that can give a thinking machine the benefits of agency should consist at least following parts:

     - A causal model of the world
     - A causal model of its own software
     - A memory that records how intents in its mind correspond to events in the outside world

> Example
>
> - I have done X=X, and the outcome was $Y=y$. But if I had acted differently, say $X=x$, then the outcome would have been better, perhaps $Y$.
> - If we are asking a machine to have the intent to do $X=x$, because aware of it, and choose to do $X=x'$ instead, we seem to be asking it to have free will

What to Think about machines?
That Think

1. Have we already made machines that think
2. Can we make machines that think
3. Will we make machines that think
4. Should we make machines that think

### Research Content

1. Thinking-oriented Knowledge Expression
   - Knowledge is the essential factor for summarizing and refining thinking
   - Existing forms of knowledge expression are not sufficient to reflect the process of thinking.
   - Knowledge expression reflecting thinking process.
2. Interaction Model of Knowledge and Thinking
   - The relationship between conscious thinking and knowledge is more specific than other consciousness.
3. Compositionality of Thinking
4. Formation Mechanism of New Knowledge and New Architecture
   - Combinable thinking → Create new knowledge

## 我的感悟

有很多打破我原有认知的观点，也有很多自己目前难以理解的知识。希望多读几遍自己可以获得更好的理解。

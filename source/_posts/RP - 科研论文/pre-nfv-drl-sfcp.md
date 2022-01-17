---
title: 【Presentation】DRL-SFCP&#58 Adaptive Service Function Chains Placement with Deep Reinforcement Learning

author: Gemini向光性
date: 2021-4-19
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

This is a presentation on our paper named "DRL-SFCP" accepted by IEEE ICC 2021, where a novel algorithm integrating GNN, Seq2Seq model and DRL is proposed.

## Document

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/1.jpg)

<!-- more -->

**Title**：DRL-SFCP: Adaptive Service Function Chains Placement with Deep Reinforcement Learning  
**Authors**：Tianfu Wang, Qilin Fan*, Xiuhua Li, Xu Zhang, Qingyu Xiong, Shu Fu and Min Gao  
**Publication**：ICC-2021 (CCF-C)  
**Direction**：Network Function Virtualization, Service Function Chain Placement  
**Keywords**：Service Function Chain Placement, Deep Reinforcement Learning, Asynchronous Advantage Actor-Critic, Graph Neural Network, Sequence-to-sequence Model  
**Innovation**：Propose a DRL algorithm integrating GNN and Seq2Seq model to capture sufficient information from input features and utilizing A3C methods to accelerate the training efficiency and model robustness.  
[paper](/resource/paper/icc-2021-drl-sfcp.pdf) | [slide](/resource/slide/icc-2021-drl-sfcp-slide.pdf)

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/2.jpg)

This presentation is divided into 5 parts.

- Firstly, I will introduce the development and significance of Network Function Virtualization.
- Secondly, the formulation of Service Function Chains Placement Problem will be provided.
- And then, I will give the description of our proposed model.
- Next, I will show some evaluation results.
- Finally, our work and some future research directions will be concluded.

## Introduction on NFV

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/3.jpg)

In the traditional network framework, with the increase of user service requests| and their diversity, the network service providers are required to invest| in expensive dedicated hardware to configure network functions| such as firewalls, video decoders and load balancers. It’s also difficult to manage and deploy.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/4.jpg)

Conversely, NFV technology offers us the ability to virtualize the network service by using virtual machines. NFV reduces dependency on dedicated infrastructure, facilitating service provision and making networks relatively more flexible. It not only can improve resource utilization and security, but also allows for flexible management and maintenance.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/5.jpg)

The global NFV market is expected to grow from USD twelve point nine billion in twenty nineteen to USD thirty six point three billion by twenty twenty four, at a CAGR (Compound Annual Growth Rate) of twenty two. nine % during the forecast period.  
The growing number of network complexities and ever-increasing demand for cloud-based services have resulted in the tremendous adoption of virtualization environments within the networking domain.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/6.jpg)

Benefiting from NFV technology, a complex network service can be composed of a series of ordered virtual network functions, known as service function chain.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/7.jpg)

In the online scenario, continuous SFC requests are attempted to be placed in the physical network under various resource constraints. It can be treated as a constrained combinational optimization problem which is also a NP-hard problem. However, it’s an challenging issue actively followed by infrastructure providers that place SFC requests as efficiently as possible to improve the resource utilization and quality of service.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/8.jpg)

The existing solutions can be classified into three categories which all have their own limitations:

- Firstly, mathematical optimization-based methods require the prior knowledge of SFCs;
- Secondly, heuristic and meta-heuristic based approaches usually fall into the local optimum;
- Thirdly, some proposed reinforcement learning-based algorithms often confront with large search space and manually selected features.

Therefore, we propose a novel deep reinforcement learning-based approach for online SFC deployment to mitigate or overcome these disadvantages.

## SFCP Formulation

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/9.jpg)

Before introducing our model, we formulate the physical network as a weighted undirected graph. Similarly, each SFC Request is modelled as a weighted directed graph. Both of them have various nodes attributes such as CPU, storage, and memory. In this paper, we only take one link attributes, bandwidth, into consideration.
There are four constraints when deploying SFC request in physical network.

- The first one is the node resources constraint: the required node resources shouldn’t exceed the remaining node resources.
- The second one is the link bandwidth constraint: the total bandwidth mapped on the node does not exceed its remaining bandwidth.
- The third one is the path constraint if the SFC is accepted, the path mapped in the physical network must pass through virtual network functions following the specified order in the request.
- The last one is the deployment constraint: When handling the same SFC request, each physical node only can accommodate one VNF.

Here, our objective is to maximize the long-term average revenue.

## Proposed DRL-SFCP Model

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/10.jpg)

Under the DRL framework, the interaction between agent and environment is defined as Markov decision process (MDP). We cast the placement of each SFC request as a finite-horizon MDP.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/11.jpg)

The physical network and SFC requests are considered as the environment. Intuitively, the state includes the current physical network features and the SFC request demands. The action is actually one of the physical nodes to accommodate one VNF. Primarily, we employ a customized neural network as the agent to make placement decisions. According to the deployment result, the calculated reward is returned to award or punish the agent.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/12.jpg)

The architecture of our model is illustrated in this figure, consisting of three main components: Embedding of physical network, Embedding of SFC request and Policy generation. Subsequently, I will explain them in detail.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/13.jpg)

Graph Neural Network has recently become a hot topic in machine learning that has been widely used in many fields. Owing to its adaption to graph structure, we use GCN to extract features of physical network. Namely, the input node features are transformed into new node Representations by GCN.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/14.jpg)

To capture orderly requirement of SFC requests, we utilize the encoder of the Seq2Seq model, which is implemented by gated recurrent unit (GRU) network. In the beginning, a sequence of VNF is input into the encoder to obtain the temporal relationship. Then, in each timestep, an action to place the current VNF will be output by the decoder.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/15.jpg)

Overall, we use GCN and the Encoder of Seq2Seq model to capture information of physical network and SFC requests. After integrating the information, the decoder will generate the action distribution to select an action. Then, this action, a physical node, will attempt to place the current VNF.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/16.jpg)

Next, the reward signal is designed to encourage the agent to place SFCs to maximize the long-term average revenue.

- When not finished and current VNF is placed successfully, the discounted SFC revenue will be award to the agent.
- When not in the end and the current VNF is placed unsuccessfully, the agent will be punished with the discounted SFC revenue.
- Particularly, when a complete SFC is placed successfully, the total SFC revenue will be returned to the agent.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/17.jpg)

Advantage actor-critic is an efficient training method for discrete action space. Both actor and critic are composed of the aforementioned neural network but have disparate output. The state, including the features of the current physical network and VNF being placed, is input into the actor network. The agent selects the action based on the probability distribution, and the environment returns the reward. Meanwhile, we can obtain a value estimating the state after the same state is input into the critic network. Then, with the help of reward and this value, the Temporal error can be calculated to optimize the actor and critic.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/18.jpg)

Besides, we use the well-known Asynchronous Advantage Actor-critic methods to enhance the training efficiency and model robustness. Briefly, There are several agents simultaneously explore in independent environments. At set intervals, experiences collected by them will be submitted to a master-worker to update global shared parameters. This parallel training architecture accelerates the training rate and Strengthen the model robustness.

## Performance Simulation

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/19.jpg)

To demonstrate the performance of our approach, we conduct a series of experiments with these settings, compared with two other algorithms. Here, the orange asterisks mean corresponding parameters will be varied in the experiments.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/20.jpg)

These figures compare our approach with other algorithms in terms of the long-term average revenue and acceptance rate, respectively.
Obviously, when the number reaches 2-thousand, our model outperforms them in the long-term average revenue and acceptance rate.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/21.jpg)

The figures provide the experiment results of three algorithms in various arrival rate conditions. Our model's impressive performance can always attain the best deployment results, which shows the excellent abilities of fitting and generalization of DNN.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/22.jpg)

In this experiment, we estimate the average processing time in different SFC lengths to test our model's scalability. As the length of SFC request increases, the average processing time of our model is slightly inferior to GRC but much faster than MCTS, which demonstrates that our model is well-suited for Application on online scenarios.

## Conclusion and Future work

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/23.jpg)

To conclude,

- we first model the SFC placement to MDP and employ an intelligent agent to guide SFC requests' online placement decisions.
- Effective neural network architecture is then leveraged to extract sufficient information from the physical network and SFC requests, integrating GNN and Seq2Seq model.
- What's more, we utilize the well-known A3C method to enhance the training efficiency and model robustness.

Additionally, more powerful neural network architectures, more efficient DRL methods and More realistic modelling scenarios are expected to be studied in future.

![ ](/resource/images/paper/icc-2021-drl-sfcp-slide/24.jpg)

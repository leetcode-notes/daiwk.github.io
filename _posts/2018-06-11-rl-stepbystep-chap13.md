---
layout: post
category: "rl"
title: "深入浅出强化学习-chap11 值迭代网络"
tags: [值迭代网络, value iteration network]
---

目录

<!-- TOC -->

- [1. 背景](#1-背景)
    - [1.1 DQN的缺陷](#11-dqn的缺陷)
    - [2.2 具有规划能力的策略网络](#22-具有规划能力的策略网络)
- [2. 值迭代网络](#2-值迭代网络)

<!-- /TOC -->



参考**《深入浅出强化学习》**

## 1. 背景


### 1.1 DQN的缺陷

先从以下几个角度来理解DQN：

+ DQN是一个深度神经网络：是一个由3个卷积层和2个全连接层组成的深度神经网络
+ DQN的训练方法是强化学习：调整神经网络权值有很多方法，只是在DQN中使用的是强化学习。详见第6章（[https://daiwk.github.io/posts/rl-stepbystep-chap6.html](https://daiwk.github.io/posts/rl-stepbystep-chap6.html)）

Tamar等发现，**已经调优的深度神经网络，很难泛化到其他游戏中，即，该网络并没有学到本质。。。**

原因就在于，DQN的网络结构是前向的多层神经网络。**输入是状态，输出是动作，也就是策略。**Tamar等人称这种策略为**『reactive policy(反应式策略)』**。也就是给定一个状态，得到一个反应动作。

从强化学习要解决的任务来看，强化学习要解决的是序贯决策问题，**即当前的决策要考虑后续的决策，使得整个策略总体最优**，而反应式策略并不能表达后续策略对当前策略的影响。。

### 2.2 具有规划能力的策略网络

所谓的规划就是**考虑后续的回报**。目前大部分强化学习所用的深度网络都是反应是网络，缺少显式的规划计算。但由于**训练方法用的是强化学习训练方法，在训练时考虑了规划问题**，所以很多网络还是比较成功的。但由于网络本身没有规划模块，所以运用到新环境时，大部分需要重新训练，即泛化能力差。

如果训练策略本身有规划模块，有以下两个好处：

+ 可以利用**已经训练好的规划模块**规划新的任务，泛化能力很强
+ 训练方法可以更灵活，不必依赖强化学习算法。**可以利用成熟的监督学习方法和模仿学习方法**。而，**如果没有数据标签时，仍然要用强化学习的训练方法**。

## 2. 值迭代网络

最常用的规划算法是值迭代算法，第3章讲了动态规划的思想。规划实际蕴含的是一个优化问题，基于贝尔曼优化原理：

`\[
\upsilon ^*(s)=\underset{a}{max}R^a_{ss'}+\gamma \sum _{s'\in S}P^a_{ss'}\upsilon ^*(s')
\]`

基于该原理，具体的算法实现是值迭代算法，在第3章中提到了[https://daiwk.github.io/posts/rl-stepbystep-chap3.html#13-%E5%80%BC%E5%87%BD%E6%95%B0%E8%BF%AD%E4%BB%A3%E7%AE%97%E6%B3%95](https://daiwk.github.io/posts/rl-stepbystep-chap3.html#13-%E5%80%BC%E5%87%BD%E6%95%B0%E8%BF%AD%E4%BB%A3%E7%AE%97%E6%B3%95)：


>1. 输入：状态转移概率`\(P^a_{ss'}\)`，回报函数`\(R^a_{ss'}\)`，折扣因子`\(\gamma\)`，初始化值函数`\(\upsilon(s)=0\)`，初始化策略`\(\pi_0\)`
>1. Repeat `\(l=0,1,...\)`
>1.    for every `\(s\)` do
>1.        `\(\upsilon ^*(s)=\underset{a}{max}R^a_{ss'}+\gamma \sum _{s'\in S}P^a_{ss'}\upsilon _l(s')\)` 
>1. Until `\(\upsilon _{l+1}=\upsilon _l\)`
>1. 输出：`\(\pi(s)=argmax_aR^a_{ss'}+\gamma \sum _{s'\in S}P^a_{ss'}\upsilon _l(s')\)`

---
title: Decision Making|人工智能、机器学习与强化学习的概述与比较
date: 2020-10-03 16:07:10
tags: 世界之内
---

之前帆帆问我，强化学习和机器学习有什么区别，我自以为清楚，但是和她讨论到最后才发现自己对这两个概念很糊涂。当时也查过资料，但是中文资料含糊不清、不够系统，大多文章是复制粘贴，作者不求甚解。

最近在了解making decision under uncertainty的背景知识，看到一篇[review](https://arxiv.org/pdf/1912.03513.pdf)，索引至作者的实验室主页，看到有一篇谈论AI、machine learning和reinforcement learning的[文章](https://castlelab.princeton.edu/what-is-ai/)，故认真学习，然后写成博客，以备不时之需。

<!--more-->

# 一、Artificial Intelligence

一般来说，有三种方法企图使用计算机实现智能：

-  Rule-based logic
-  Making estimates using data from the environment
-  Making decisions that interact with the environment

**1）Rule-based logic**

利用逻辑推理，基于一系列前提条件，利用规则得到结论。类似于多次使用“if...else”语句进行判断、选择。

**2）Making estimates using data from the environment**

这个问题往往使用Machine Learning方法，而Machine Learning与统计学紧密相关，也被称为统计学习，或者更广泛地说，数据科学。Machine Learning分为两类：

- supervised learning

使用带标签的数据训练machine learning 模型。

- unsupervised learning

对不带标签的数据进行聚类。

**3）Making decisions that interact with the environment**

简单地说，making decision的方法分为以下几类：

- Rule-based logic
- Deterministic optimization
- Reinforcement learning
- Stochastic optimization

# 二、Making estimates using data from the environment

所谓的estimates，有三种形式（目的）：

- **classification**：判断input的类型。
- **inference**：对input进行推断。
- **prediction**：基于input，进行预测。

三种任务都基于一个共同的框架：已知输入 $x$，通过统计模型$f(.|\theta)$，得到相应的结果 $f(x|\theta)$。为了得到一个效果不错的模型，需要通过最小化模型输出和实际结果的方法，训练模型。

统计模型可以分为三类：

- **Lookup table**
- **Parametric models**
- **Nonparametric/locally parametric** 

# 三、Making decisions that interact with the environment

making decisions的目的，可以分为两类：

- **Decisions that change the environment.** 所做的决策会改变环境。
- **Decisions to exchange information.** 所做的决策是为了交换信息。而该决策又可以分为两类：
  - **Decisions to acquire information** 所做决策是为了获取信息。（比如simulation）
  - **Decisions to communicate/disseminate information.** 所做的决策是为了传递信息，对环境没有任何互动。

实际上，大部分决策既会改变环境，又会交换信息。因为在大部分情况下，改变环境之后，当前的信息肯定会变化，比如说蝴蝶效应，蝴蝶扇一扇翅膀（决策），就会引起龙卷风（改变环境），龙卷风本身也是一种信息，会影响之后的决策。

为了更清晰地表述making decisions，下面将引入一些符号来表示决策过程中的重要概念。“state”，即状态，包含了所有决策所需要的信息，用$S_t$表示。$S_t$ 可以是物理信息$R_t$ 比如汽车的位置、速度，水库的水量等等；也可以是其他的信息$I_t$，比如今天的天气情况；或者是某些我们无法观察到的数量的置信 (beliefs) $B_t$。

什么叫置信 $B_t$？可以理解成先验，比如数据的均值、方差，比如参数的分布等。所以，纯粹的Decisions to acquire information，因为获得了环境信息，所以对环境的先验知识得到了丰富，因此改变的只有$B_t$，这是一个learning problem，只是为了学习$B_t$。关于学习$B_t$的问题，被称为“多臂老虎机” (multiarmed bandit) 问题。当然，大部分情况下，为了获取$B_t$而making decision之后，环境状态的$B_t$会随之变化，相关问题被称为 *active learning problem.*

还有几个概念。我们使用$C(S,x)$来表示cost function（或者称为contribution function ）。所谓的$C(S,x)$，是用来衡量在状态$S$下，决策$x$的好坏。making decision的目的就是选择最好的$x$，即使得$C(S,x)$最大的$x$。$C(S,x)$是用来衡量决策好坏的，这个评估直接影响决策的选择。我们使用$X^\pi(S)$来表示决策选择函数，$x_t=X^\pi(S_t)$。making decision的核心就是得到最优的决策选择函数$X^\pi(S)$。

## （一）什么是reinforcement learning？

强化学习（reinforcement learning）是making decision的一个方法之一，但是这几年热度很高。所有的强化学习方法，都基于“Markov decision processes”。强化学习的核心思想是Q-learning。以下是Q-learning的公式表示：
$$
\begin{array}{l}
\hat{q}^{n}\left(s^{n}, a^{n}\right)=r(s^n, a^n)+\max _{a^{\prime}} \bar{Q}^{n-1}\left(s^{n+1}, a^{\prime}\right) \\\\
\bar{Q}^{n}\left(s^{n}, a^{n}\right)=(1-\alpha) \bar{Q}^{n-1}\left(s^{n}, a^{n}\right)+\alpha \hat{q}^{n}\left(s^{n}, a^{n}\right)
\end{array}
$$
$r(s^n, a^n)$是奖励函数，评估在状态$s^n$下进行动作$a^n$的好坏。$\alpha$是平滑系数，用来平衡$Q(s,a)$和$q(s,a)$对最终结果的影响程度。

Q-learning的思想，简单地说，就是在选定决策$a^n$的情况下，计算该决策对下一个状态$s^{n+1}$的影响，再把这种影响计入对该决策好坏的评定。强化学习方法通过计算$Q(s,a)$来评估决策的好坏，最好的决策能够最大化$Q(s,a)$。

## （二）reinforcement leanring与machine leanring的交集

换一个角度看machine learning，比如说，如果making decision的目的是为了模仿人的行为，那么人在每一个时间单位的状态$s$，其实就是$x$；在每一个状态下的行为$a$，其实就是$y$，machine learning模型的目的，就是学习如何将$x$映射成$y$，即在状态$s$下做出怎样的行为$a$。

但是这仅仅是“**模仿**”，所谓的making decision，目的在于将决策的performance最大化，而衡量、最大化performance的过程不需要标签$y$，简单地说，模仿是无法实现决策最优的。

## （三）什么是stochastic optimization？

stochastic optimization描述的问题是：进行决策，并根据决策后的环境状态选择下一步决策，但是观察环境状态的行为又会影响到环境状态，从而影响决策。数学化表示，有以下情况：

- Make decision $x$, see information $W$, stop.
- Make decision $x^0$, see information $W^1$, make another decision $x^1$, stop.
- Sequential decisions and information: $S^{0}, x^{0}, W^{1}, S^{1}, x^{1}, W^{2}, S^{2}, \ldots, W^{N}, S^{N}, x^{N}$ where $S^n$ is the state variable, which captures everything we need to determine $x^n$. Note that state variables may include beliefs about unobservable parameters.
- Infinite horizon problems, where $N→∞$, and where the information $W^n$ comes from a stationary distribution.

stochastic optimization的核心问题是，寻找到一个决定$x^n$（写作$X^{\pi}\left(S^{n}\right)$）的策略选择算法$\pi$，从而最大化cumulative reward：
$$
\max _{\pi} \mathbb{E}\left(\sum_{n=0}^{N} C\left(S^{n}, X^{\pi}\left(S^{n}\right)\right) \mid S^{0}\right)
$$
或者final reward
$$
\max _{\pi} \mathbb{E} F\left(x^{\pi, N}, \hat{W}\right)
$$
其中$x^{\pi, N}$是指在策略搜索算法$\pi$下$N$次迭代后的策略结果集。

有一点需要注意，接下来将详细分析。整个优化过程如下：

1. 通过策略选择算法$\pi$，选择策略$x^{\pi, N}$。
2. 计算 cumulative reward 或者 final reward，评估决策好坏。
3. 优化策略选择算法$\pi$。
4. 重复以上操作，直到 cumulative reward 或者 final reward 最大。

需要注意的点是：已知策略选择算法$\pi$，我们该如何选择每一个策略$x^t$，从而得到策略集$x^{\pi, N}$？

可以看到，stochastic optimization的核心问题是通过最大化reward，来优化策略选择算法$\pi$。而该核心问题的核心是，如何通过策略选择算法$\pi$，选择每一个策略$x^t$，从而形成需要评估的策略集$x^{\pi, N}$，这个问题称为searching over policies。

searching over policies和machine learning中对统计模型的选择有些相像。具体说，machine learning基于cost function来改变模型的参数，从而达到对模型的选择。而策略选择则更加复杂，用什么评判标准来选择每一个policy，在下一节进行讨论。

## （四）The classes of policies for stochastic optimization

有2种基本的策略选择方法，而这两种方法各自又可以分成2类：

1. **The policy search class:** policies that have to be tuned over time to work well in terms of the performance metric (**My opinion:** find a function ,which determines the policy, that can make <u>a set of policies</u> that maximize the performance metric)
   - Policy function approximations (PFAs)
     - map state to action
   - Cost function approximations (CFAs)
     - parametrically modified optimization problems (to optimize cost function)

该类简单地说，就是选择对整体效果最好的策略，而不考虑每一个策略对整一个决策链的影响。

2. **The lookahead class:** These policies work by finding the action now, given the state we are in, that maximizes the one-period reward plus an approximation of the downstream value of the state that the action takes us to. (modeling the downstream impact of a decision made now on the future.) (**My opinion:** find a function, which determines the policy, that can make <u>every policy</u> based on current state which have the best performance for the following policies. Like greedy algorithm)
   - Policies based on value function approximations (VFAs)
     -  estimate the value of being in this new state, choose the decision that makes the biggest value  (like Q-learning)
   - Direct lookaheads (DLAs) 
     - make several actions and update them periodically

该方法的思想类似于贪婪思想，searching over policies的策略是，寻找每一个对之后决策积极影响最大的决策。

# 三、Reference

- Powell, Warren B. "What is AI?" https://castlelab.princeton.edu/what-is-ai/ 
- Powell, Warren B. "From Reinforcement Learning to Optimal Control: A unified framework for sequential decisions." *arXiv preprint arXiv:1912.03513* (2019).


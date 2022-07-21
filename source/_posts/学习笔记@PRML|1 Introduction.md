---
title: 学习笔记@PRML|1 Introduction
date: 2020-07-31 21:40:19
tags: 世界之内
---

Notes of *Pattern Recognition and Machine Learning (PRML)*

第一章为开篇，讨论了模式识别与机器学习的三个理论基础（从贝叶斯派的角度）：概率论、决策论和信息论。

概率论的目的是量化和利用不确定性，是模式识别的第一阶段：Inference stage。决策论的目的是在概率论的框架下，根据已知的结论和约束，进行决策，是模式识别的第二阶段：Decision stage。信息论的目的是衡量对象包含的信息量。

<!--more-->

> The field of pattern recognition is concerned with the **automatic discovery** of regularities in data through the use of **computer algorithms** and with the use of these regularities to **take actions** such as classifying the data into different categories.
>
> By Bishop in *Pattern Recognition and Machine Learning*

# 1.2节 概率论

## 一、贝叶斯派与频率派观点的不同

> **频率学派**，其特征是把需要推断的参数θ视作固定且未知的常数，而样本X是随机的，其着眼点在样本空间，有关的概率计算都是针对X的分布。
>
> **贝叶斯学派**，他们把参数θ视作随机变量，而样本X是固定的，其着眼点在参数空间，重视参数θ的分布，固定的操作模式是通过参数的先验分布结合样本信息得到参数的后验分布。
>
> （作者：秦松雄，链接：https://www.zhihu.com/question/20587681/answer/23060072）

> **频率学派和贝叶斯学派最大的差别其实产生于对参数空间的认知上。**所谓参数空间，就是你关心的那个参数可能的取值范围。频率学派（其实就是当年的Fisher）并不关心参数空间的所有细节，他们相信**数据都是在这个空间里的”某个“参数值下产生的**（虽然你不知道那个值是啥），所以他们的方法论一开始就是从“哪个值最有可能是真实值”这个角度出发的。于是就有了最大似然（maximum likelihood）以及置信区间（confidence interval）这样的东西，你从名字就可以看出来他们关心的就是我有多大把握去圈出那个**唯一的真实参数**。而贝叶斯学派恰恰相反，他们关心**参数空间里的每一个值**，因为他们觉得我们又没有上帝视角，怎么可能知道哪个值是真的呢？所以参数空间里的**每个值都有可能是真实模型使用的值，区别只是概率不同而已**。于是他们才会引入先验分布（prior distribution）和后验分布（posterior distribution）这样的概念来设法找出参数空间上的每个值的概率。
>
> （作者：Xiangyu Wang，链接：https://www.zhihu.com/question/20587681/answer/41436978）

贝叶斯有“先验分布”的概念。而贝叶斯派的缺点也是从先验分布而出，先验分布的选择会影响结果，一般都是用高熵高斯分布或者均匀分布。

两派的不同，导致了最大似然估计和最大后验估计，比较两者公式，最大似然估计假设参数θ已知，求x的后验分布，最大后验估计则相反。

## 二、参数估计的三种方法：

1. 最大似然估计MLE
2. 最大后验估计MAP
3. 全贝叶斯方法
   1. $p( t| x, X, t)=∫p(t| x, w)p(w| X, t)dw $
   2. 其实是用参数 $w$ 的后验概率为权，对 probability 进行一次加权平均;因此这个过程需要 对 $w$ 进行积分，即 marginalization。 

最大似然估计和最大后验估计都是点估计，最大后验估计相当于最大似然估计加上正则项。（参考博文：https://blog.csdn.net/u011508640/article/details/72815981）

全贝叶斯方法实现起来有困难：Marginalization问题，即需要对全参数空间进行积分，但是算力不够，样本也不够。解决方法有两个：用monte carlo方法进行采样；用deterministic approximation （不知道是什么）

## 三、线性回归与最大似然估计、最大后验估计的比较

### （一）线性回归作为最大似然

现在的目的是为了得到条件概率$p(y|x)$，即已知$x$，得到$y$的概率。假设该条件概率符合正态分布，其中$y_hat(x;w)$为正态分布的均值，那么条件对数似然的表达式，形式与均方误差相同。（见《深度学习》p117）

### （二）线性回归作为最大后验

最大后验$log p(θ|x)=log p(x|θ) + log p(θ)$，右边两项的第一项，就是最大似然估计里的条件似然。

右边两项的第二项，即参数$θ$的先验。当先验是正态分布，其均值为0，方差为$(1/λ)(I^2)$时，其对数形式，就正比于l2正则化表达式：$λ(w^T)w$。

所以，具有高斯先验权重的MAP 贝叶斯推断对应着权重衰减。 

# 1.3节 模型选择

选择合适复杂度的模型：

1. 验证集
2. 交叉验证法、留一法

对模型表现的度量：

1. 赤池信息准则（Akaike information criterion ）
2. 赤池信息准则的变体：贝叶斯信息准则

# 1.4节 维度灾难

维度灾难：随着维度变高，物体的体积将会越来越集中在物体表面附近的区域里。因此，不利于进行模式识别，

但是这个问题可以被解决，因为有两个规律：

1. 高维度数据一般聚集在较低维度的空间里；
2. 真实数据一般比较光滑，x的轻微改变也只会导致y的轻微改变，所以可以用比如插值的方法，来进行预测。

# 1.5节 决策论

## 一、定义

解决问题的两个阶段：

1. Inference stage：概率论提供数学框架对不确定性进行量化和控制。即，得到联合概率分布或者后验概率分布
2. Decision stage：决策论在概率论提供的理论框架下，进行决策。即，根据联合概率分布或者后验概率分布，进行合适的决策（比如分类、回归等）。

## 二、如何做决策（在分类问题下）

### （一）misclassification loss

计算错分类的概率，通过最小化misclassification loss，选择最优的decision boundary，类似于阈值的概念。

### （二）expected loss

有两类misclassification：把positive分类成negative，把negative分类成positive。这两类错误的后果可能是不同的，例如“把癌症诊断为无癌症”的后果比“把无癌症诊断为癌症”的后果更严重，又如“把正常邮件诊断为垃圾邮件”的后 果比“把垃圾邮件诊断为正常邮件”的后果更严重。这时候，少犯前一错误比少犯后一错误更 有意义。为此需要 loss function 对不同的错误的代价做量化。 不同错误的代价或者权重，记录在Loss matrix中。

## 三、三种Inference、Decision的方法或观点（在分类问题下）

1. generative model：第一步，计算 类条件概率函数①（class-conditional densities，根据贝叶斯公式，相当于求得了联合概率分布），第二步，计算后验概率分布，第三步，选择策略。
2. discriminant model：第一步，计算后验概率分布，第二步，选择策略。
3. discriminant function：直接求输入和输出的映射。（比如说神经网络）

①类条件概率函数p(X|w_i)是指在已知某类别的特征空间中，出现特征值 X 的概率密度，指第w_i类样品中其属性 X 是如何分布的。

## 四、三种方法的优缺点

1. generative model：求得联合概率分布，就可以求得x的分布，从而有利于离群点发现。但是如果只是针对分类问题，联合概率分布没必要，很浪费计算资源。
2. discriminant function：端对端，不用求后验概率分布。但是后验概率分布很重要，在以下情况中，后验概率分布很有用：
   1. Compensating for class priors。在unbalanced data中，可以在其中取部分data组成balanced data，计算balanced data的后验概率分布，根据贝叶斯公式，只要将balanced data的后验概率除以balanced data的先验概率分布，再乘以unbalanced data的先验概率分布，就能得到unbalanced data的后验概率分布。
   2. Loss matrix改变之后，需要从0开始重新求discriminant function。但是如果知道了后验概率分布，只需要重新求expected loss，再最小化loss就可以了。
   3. Combining models。多个数据集，比如CT的图像数据集，和血糖血脂的数值累数据集，可以分别求这两个数据集的后验概率分布。假如两者条件独立，则直接把两个后验分布概率相乘，就能得到两个数据集放在一起后的后验分布概率。但是discriminant function完全无法实现。

以上讨论，再一次让我感受到了贝叶斯视角下的概率模型有多么优雅。

# 1.6节 信息论

## 一、信息熵的由来

1. 信息熵数学公式的描述（intuitively speaking）：
具体的例子：在看一段文字X后，能否预测到X之后的文字内容。越容易预测，则说明X包含的信息越少，越不容易预测，则说明X包含的信息越多。用概率的视角去看，这个例子实际上描述的是X的不确定性，可以用概率来描述：$P(X)$。所以，直观上说，信息量$h(X)$和$P(X)$相关。

再考虑两个变量$X$、$Y$，如果$X$、$Y$不相关，那么$h(X, Y) = h(X) + h(Y)$，但是$P(X, Y) = P(X)P(Y)$。所以可以考虑，$h(X)$是不是和$log P(X)$成正比，因为$log P(X, Y) = log P(X) + log P(Y)$。

所以就定义$h(X) = log P(X)$。
2. 直观的理解：信息熵是衡量信息量的指标。
3. 信息学上的理解：```Entropy is a lower bound on the number of bits needed to transmit the state of a random variable.```
4. 两类数据：
   1. 离散型数据的信息熵：当变量的每个取值概率相同时，信息熵最大。
   2. 连续型数据的信息熵：当变量遵循高斯分布时，信息熵最大。

## 二、熵的相关概念

1. 条件熵：在已知$X$的情况下，需要额外的信息去确定$Y$，这个额外的信息被称为条件熵。
2. 相对熵：设有一个未知的分布$ p(x)$，而 $q(x)$为我们所获得的一个对 $p(x)$的近似; 按照 $q(x)$ (而非真实分布 $p(x)$)对该随机变量的各个值进行编码，平均编码长度比用真实分布 $p(x)$进行编码要额外长多少?答案是相对熵(KL 散度)$KL(p||q)$。 
3. 互信息：如果两个随即变量 $X$,$Y$ 是独立的，那么有$ p(x, y)=P(x)P(y)$;当二者并不独立时，我们希望可以度量它们离独立还有多远，这个度量就是互信息。
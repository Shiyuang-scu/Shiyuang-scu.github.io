---
title: 【Introduction to TensorFlow】01 TF 快速入门
date: 2019-10-29 15:38:22
tags: 
 - 机器学习
 - 世界之内
---



本文内容不包括在课程《Introduction to TensorFlow》中，但是因为学习任何东西，首先都得学习其基本概念，所以本文介绍了TensorFlow的基本概念，主要介绍了Graph、Session两个概念。

本文极大程度借鉴了小土刀的《【CS20-TF4DL】01 Tensorflow 快速入门》

- **原文文作者：** wdxtub
- **原文链接：** http://wdxmzy.com/course/cs20-01/2019/08/03/

<!--more-->

# 基本概念

> 什么是TensorFlow？

在TensorFlow的官网，可以看到如下定义：

TensorFlow is an open source software library for numerical computational using data flow graphs

> 为什么用TensorFlow？

选择TensorFlow，是因为：

- 拥有Python API
- 可使用单个API就能将计算部署在多个CPU、GPU、服务器或者移动设备上
- 可视化，使用TensorBoard
- 拥有庞大的社区
- 自动微分

> 学习TensorFlow有何参考书？

TensorFlow一直在更新，出版的书跟不上最新版本，但是有如下参考书目：

- Aurélien Géron’s Hands-On Machine Learning with Scikit-Learn and TensorFlow (O’Reilly,March 2017)
- François Chollet’s Deep Learning with Python (Manning Publications, November 2017)
- Nishant Shukla’s Machine Learning with TensorFlow (Manning Publications, January 2018)
- Lieder et al.’s Learning TensorFlow A Guide to Building Deep Learning Systems (O’Reilly, August 2017)

开始正式学习。

------------

宇宙之初：

<center>import tensorflow as tf</center>
lol 

# Tensor

Tensor 张量，可以理解为一个 n 维的数组，其中低维的张量有另外的名字：

- 0 维张量称为标量 Scalar，也就是数字
- 1 维张量称为向量 Vector
- 2 维张量称为矩阵 Matrix
- 再高维的就叫张量

# Graph and Sessions

首先介绍一个TensorFlow最重要的概念，Data Flow Graph，数据流图。通过数据流图，计算（computation）被分离为定义和计算，Graph负责定义整个计算框架，Session负责真正执行这个计算。

举一个例子便于理解，如图：

![img](https://lh3.googleusercontent.com/p1NjrScNzsleXk3BSrD4JmNLZvRaL1VipO_pWBm0SCoJO-hJXDV2xdsFSdWXhDHOI-a-I_Zk6faBC_lMotvT9zU_esNdWToJwvAvgJumk7yWoHODUI3dlzhaOOo2fCveQTAz0xTOiIE)

我们要计算$(a+b)+a \times b$，可以构建一个这样的框架，也就是计算流图。

这个构建不需要真的数据，我们只是定义了一个规则。当我们需要计算$(5+3)+5 \times 3$时，就通过session 来执行这个计算过程。5和3通过session的帮助，像两股水流，流入我们之前定义的河道中，最终经过一系列的节点，流出结果。

因此，由上面的例子可以总结出，TensorFlow的计算有两个基本步骤：

1. 构建一个Graph
2. 使用Session执行由Graph定义的操作



概念讲完，就开始coding了。

```python
import tensorflow as tf
a = tf.add(3, 5)
# 在 Tensorboard 的显示里，节点表示操作、变量和常量，边表示张量
print(a)
# 显示 Tensor("Add:0", shape=(), dtype=int32)
# 那么我们如何得到 a 的值呢，需要创建一个 session，然后对图进行计算，像这样
sess = tf.Session()
print(sess.run(a)) # run 之后就得到 a 实际的值
sess.close() # 注意要关闭

# 每次都关闭太麻烦，建议用 with 来进行
with tf.Session() as sess:
    print(sess.run(a))
# session 对象封装了用于处理 Operation 和 Tensor 的环境（分配内存、处理计算等）

# 一个更复杂的图，结构如下图所示
x = 2
y = 3
add_op = tf.add(x, y)
mul_op = tf.multiply(x, y)
useless = tf.multiply(x, add_op)
pow_op = tf.pow(add_op, mul_op)
with tf.Session() as sess:
    z = sess.run(pow_op) # 只计算 pow_op，useless 节点不会进行计算
    # 我们也可以同时计算多个，因为本质上我们传入的是需要获取的操作列表，也就是 fetches，是一个 List
    # tf.Session.run(fetches, feed_dict=None, options=None, run_metadata=None)
    z, not_useless = sess.run([pow_op, useless])
```

上述代码在TensorFlow的Graph如下：

![img](https://lh6.googleusercontent.com/oLEKzrdeaeSrgOcHFgWyxW2eaoHXqMUQet00CfGPitoDqxGQirDR4S84JojiQPMRrimhUXFecJDWpzBCmGu-sw1ULAglyinPfHU7ldm2dVG5DoNuUJWq8vXa4utay_l06jwUcjSKLnQ)

TensorFlow将计算过程表示为Graph，有一个重要的优势，可以知道每一个计算的关系，TF 就可以充分利用计算资源，有效地分配计算任务。

我知道计算Pow需要Add和Mul，那么我可以同时计算Add和Mul，而不是随着代码的顺序执行计算。此外，因为我只需要Pow，所以Mul_1(useless节点)就不会进行计算。

以上的计算都是在同一个 Graph 进行的，那么如果想要创建多个 Graph 可以吗？可以，但没必要，也不推荐，原因如下：

- 没必要：多个 Graph 就需要多个 session，每个 session 默认是会占用所有可用资源
- 不推荐：多个 Graph 之前只能通过 python/numpy 传输数据，这是无法进行分布式计算的
- 没必要：如果需要多个 subgraph，放到一个 graph 中，然后不要相连就好

最后总结一下为什么要用 Graph 机制来处理计算，因为：

- 节省计算资源。通过图结构的最短路径来执行计算，获取所需的值
- 能够把计算分成小块且可微的任务，更好的利用自动微分
- 可以利用分布式计算，把计算任务放到多个 CPU/GPU/TPU 上
- 很多机器学习模型是通过有向图来进行训练和可视化的，非常契合，如下图：

![img](https://lh3.googleusercontent.com/hC0XsNqxHqMcTT3Bqz-_Omu-xp9ZFjwiWej2RygzE0wy90ws1QQpme5SZYly3fLg-XdS5N5uBN7HqegCo3MZd-eJQJRd1ycpGMqBWbhOcE_x0O_LsAVRPzx4DMteAbmNs5FjFV1QNR8)

- A neural net graph from Stanford’s CS224N course